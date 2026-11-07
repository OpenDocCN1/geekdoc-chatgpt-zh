```
print("Train Finished takes:", duration)
# 画出准确率执行的結果
import matplotlib.pyplot as plt
plt.plot(epoch_list, accuracy_list,label="accuracy" )
fig = plt.gcf()
fig.set_size_inches(4,2)
plt.ylim(0.8,1)
plt.ylabel('accuracy')
plt.xlabel('epoch')
plt.legend()
plt.show()
# 使用 test 测试数据集评估模型的准确率
print("Accuracy:",
      sess.run(accuracy,feed_dict={x: mnist.test.images,
                                  y_label: mnist.test.labels}))
# 进行预测
prediction_result=sess.run(tf.argmax(y_predict,1),
                          feed_dict={x: mnist.test.images ,
                                    y_label: mnist.test.labels})
print(" 查看预测结果的前 10 项数据 ")
print(prediction_result[:10])
print(" 查看预测结果的第 248 项数据 ")
print(prediction_result[247])
print(" 查看测试数据的第 248 项数据 ")
import numpy as np
print(np.argmax(mnist.test.labels[248]))
# 找出预测错误的
print(" 找出预测错误的 ")
for i in range(500):
    if prediction_result[i]!=np.argmax(mnist.test.labels[i]):
        print("i="+str(i)+
              "   label=",np.argmax(mnist.test.labels[i]),
              "predict=",prediction_result[i])
# 保存模型
saver = tf.train.Saver()
save_path = saver.save(sess, "saveModel/tensorflow_mlp_model1")
print("Model saved in file: %s" % save_path)
# 关闭会话
sess.close()
#####################################################
```

训练结果如下：

Train Epoch: 01 Loss= 1.592497230  Accuracy= 0.892
Train Epoch: 02 Loss= 1.542448878  Accuracy= 0.934
Train Epoch: 03 Loss= 1.526387930  Accuracy= 0.9436
Train Epoch: 04 Loss= 1.509701014  Accuracy= 0.9582