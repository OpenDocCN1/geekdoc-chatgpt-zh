https://www.movingai.com/gdc14/

# 7.10 实现无悔匹配算法

继续老虎机算法，我们将对 UCB1 算法做一些改进，叫作无悔匹配。我们将使用石头剪刀步中的同样案例，但是也可以用于其他类型的游戏，比如战斗游戏。

# 准备工作

阅读上一节并考虑成员变量和数据结构很重要，上一节中成员函数与本节算法并不相关，因为本节中我们要实现不同的函数，但仍然基于上一节中的知识点。

# 操作步骤

我们将在之前创建的 Bandit 类中实现下面的代码：

+   1. 定义要用到的变量:
+   float initialRegret = 10f;
+   float[] regret;
+   float[] chance;
+   RPSAction lastOpponentAction;
+   RPSAction[] lastActionRM;

+   2. 定义用于初始化的成员函数:
+   public void InitRegretMatching()
+   {
+       if (init)
+           return;
+       // next steps
+   }

+   3. 声明局部变量并初始化:
+   numActions = System.Enum.GetNames(typeof(RPSAction)).Length;
+   regret = new float[numActions];
+   chance = new float[numActions];
+   int i;
+   for (i = 0; i < numActions; i++)
+   {
+       regret[i] = initialRegret;
+       chance[i] = 0f;
+   }
+   init = true;

+   4. 定义成员函数，用于计算下一步的行动:
+   public RPSAction GetNextActionRM()
+   {
+       // next steps
+   }