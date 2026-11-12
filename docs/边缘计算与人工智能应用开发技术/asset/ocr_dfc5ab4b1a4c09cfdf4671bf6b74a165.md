```
callbacks.run('on_train_end', last, best, plots, epoch, results)
LOGGER.info(f"Results saved to {colorstr('bold', save_dir)}")
torch.cuda.empty_cache()
return results
```

# 3) 训练测试

detect.py是用于训练完成测试模型文件，代码如下：