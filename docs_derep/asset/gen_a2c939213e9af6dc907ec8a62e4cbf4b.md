YVM硬件适配与JIT模拟演示

这篇演示围绕一套极简虚拟机 YVM，如何根据运行时的热点统计配合 CPU 特性做自适应优化展开。思路很直接：先用解释器跑字节码，统计执行次数达到阈值后伪装一次 JIT，根据检测到的指令集把 ADD、MUL 切换到快速模式，并在多线程下同时跑两段示例程序验证效果。

上下文对象负责承载执行状态。里面保存指令指针、是否结束标志、循环计数、是否已做过优化的开关，以及一个用于触发伪 JIT 的执行计数器。构造时指针归零、结束标志关闭、循环计数归零、优化标志关闭、计数归零，并预留 1024 的栈空间。

JIT 模拟只关心两种伪加速开关：是否使用 SIMD 加法、是否使用 SIMD 乘法。判断逻辑很简单，检测到 SSE、AVX、AVX2 之一就把两个开关打开，其余情况保持关闭。

解释器采用标准的 fetch-decode-execute 循环。每次取当前指令执行后指针自增，指令指针超出代码长度时强制结束。执行过程中每条指令都会让执行计数自增，一旦计数超过热度阈值且尚未优化，就调用一次 CPU 特性探测，随后根据探测结果生成新的加速信息并写入运行状态，同时标记为已优化。开启详细日志时会打印触发时刻的特性编号和两个开关状态。

下面是两段用于验证的字节码构造，以及多线程工作者和入口程序的完整实现。

[PRE0]cpp
std::vector<YVMInstruction> createProgram1() {
    // 程序功能：计算(3+4)*2后打印，然后循环执行几次加法，最后结束
    // 在中间插入SLEEP_MS等操作，让多线程并行更明显
    // 另外插入一条JIT_HINT指令，表明这里可以考虑用JIT
    std::vector<YVMInstruction> code;
    code.reserve(50);
    // PUSH_INT 3
    code.push_back({YVMOpcode::PUSH_INT,3});
    // PUSH_INT 4
    code.push_back({YVMOpcode::PUSH_INT,4});
    // ADD => (3+4)
    code.push_back({YVMOpcode::ADD,0});
    // PUSH_INT 2
    code.push_back({YVMOpcode::PUSH_INT,2});
    // MUL => (3+4)*2
    code.push_back({YVMOpcode::MUL,0});
    // PRINT => 输出14
    code.push_back({YVMOpcode::PRINT,0});
    // JIT_HINT => 提示后续可能会使用JIT
    code.push_back({YVMOpcode::JIT_HINT,0});
    // LOOP_START param=3 => 循环3次
    code.push_back({YVMOpcode::LOOP_START,3});
    // PUSH_INT 10
    code.push_back({YVMOpcode::PUSH_INT,10});
    // PUSH_INT 20
    code.push_back({YVMOpcode::PUSH_INT,20});
    // ADD => 30
    code.push_back({YVMOpcode::ADD,0});
    // PRINT => 输出30
    code.push_back({YVMOpcode::PRINT,0});
    // SLEEP_MS 50
    code.push_back({YVMOpcode::SLEEP_MS,50});
    // LOOP_END
    code.push_back({YVMOpcode::LOOP_END,0});
    // END
    code.push_back({YVMOpcode::END,0});
    return code;
}
[PRE1]cpp
std::vector<YVMInstruction> createProgram2() {
    // 功能：先 PUSH_INT 1, PUSH_INT 2, ADD => 3, 打印
    // 然后LOOP_START(2) => 每次PUSH_INT 5, ADD, 打印
    // 结束
    std::vector<YVMInstruction> code;
    code.reserve(30);
    code.push_back({YVMOpcode::PUSH_INT,1});
    code.push_back({YVMOpcode::PUSH_INT,2});
    code.push_back({YVMOpcode::ADD,0});
    code.push_back({YVMOpcode::PRINT,0}); // 输出3
    code.push_back({YVMOpcode::LOOP_START,2});
    // 每次循环栈顶都加5
    code.push_back({YVMOpcode::PUSH_INT,5});
    code.push_back({YVMOpcode::ADD,0});
    code.push_back({YVMOpcode::PRINT,0});
    code.push_back({YVMOpcode::SLEEP_MS,30});
    code.push_back({YVMOpcode::LOOP_END,0});
    // END
    code.push_back({YVMOpcode::END,0});
    return code;
}
[PRE2]cpp
void yvmThreadWorker(const std::vector<YVMInstruction>& code,int threadId) {
    if (ENABLE_VERBOSE) {
        std::cout << "[THREAD " << threadId << "] 开始执行YVM程序...\n";
    }
    YVMContext ctx;
    JITInfo jitInfo(false,false);
    YVMInterpreter interpreter(code);
    interpreter.run(ctx,jitInfo);
    if (ENABLE_VERBOSE) {
        std::cout << "[THREAD " << threadId << "] 结束YVM程序\n";
    }
}
[PRE3]cpp
int main() {
    std::cout << "============ YVM Hardware Adapt & Optimize Demo ============\n";
    // 先模拟一下硬件特性检测
    CPUFeature feature=detectCPUFeature();
    std::cout << "CPUFeature detected: ";
    switch(feature) {
        case CPUFeature::NONE:
            std::cout << "NONE\n"; break;
        case CPUFeature::SSE:
            std::cout << "SSE\n"; break;
        case CPUFeature::AVX:
            std::cout << "AVX\n"; break;
        case CPUFeature::AVX2:
            std::cout << "AVX2\n"; break;
    }
    // 创建两段字节码
    auto code1=createProgram1();
    auto code2=createProgram2();
    std::thread t1(yvmThreadWorker,std::cref(code1),1);
    std::thread t2(yvmThreadWorker,std::cref(code2),2);
    t1.join();
    t2.join();
    std::cout << "============ All YVM Threads Finished ============\n";
    return 0;
}
[PRE4]
============ YVM Hardware Adapt & Optimize Demo ============
CPUFeature detected: AVX2
[THREAD 1] 开始执行YVM程序...
[DEBUG] PUSH_INT 3
[DEBUG] PUSH_INT 4
[DEBUG] ADD => 3+4=7
[DEBUG] PUSH_INT 2
[DEBUG] MUL => 7*2=14
[DEBUG] PRINT
[OUTPUT] 14
[DEBUG] JIT_HINT encountered.
[DEBUG] LOOP_START param=3
[DEBUG] PUSH_INT 10
[DEBUG] PUSH_INT 20
[DEBUG] ADD => 10+20=30
[DEBUG] PRINT
[OUTPUT] 30
[DEBUG] SLEEP_MS 50
[DEBUG] LOOP_END -> loopCounter=3
[DEBUG] LOOP_START found => jump back
[DEBUG] PUSH_INT 10
[DEBUG] PUSH_INT 20
[JIT] Hot threshold reached! CPUFeature=3,fastAdd=1,fastMul=1
[DEBUG] ADD => 10+20=30
[DEBUG] PRINT
[OUTPUT] 30
[DEBUG] SLEEP_MS 50
[DEBUG] LOOP_END -> loopCounter=2
[DEBUG] LOOP_START found => jump back
[DEBUG] PUSH_INT 10
[DEBUG] PUSH_INT 20
[DEBUG] fastAdd enabled => SSE/AVX(伪)
[DEBUG] ADD => 10+20=30
[DEBUG] PRINT
[OUTPUT] 30
[DEBUG] SLEEP_MS 50
[DEBUG] LOOP_END -> loopCounter=1
[DEBUG] END
[THREAD 1] 结束YVM程序
[THREAD 2] 开始执行YVM程序...
[DEBUG] PUSH_INT 1
[DEBUG] PUSH_INT 2
[DEBUG] ADD => 1+2=3
[DEBUG] PRINT
[OUTPUT] 3
[DEBUG] LOOP_START param=2
[DEBUG] PUSH_INT 5
[DEBUG] ADD => 3+5=8
[DEBUG] PRINT
[OUTPUT] 8
[DEBUG] SLEEP_MS 30
[DEBUG] LOOP_END -> loopCounter=2
[DEBUG] LOOP_START found => jump back
[DEBUG] PUSH_INT 5
[JIT] Hot threshold reached! CPUFeature=3,fastAdd=1,fastMul=1
[DEBUG] fastAdd enabled => SSE/AVX(伪)
[DEBUG] ADD => 8+5=13
[DEBUG] PRINT
[OUTPUT] 13
[DEBUG] SLEEP_MS 30
[DEBUG] LOOP_END -> loopCounter=1
[DEBUG] END
[THREAD 2] 结束YVM程序
============ All YVM Threads Finished ============