```
[34]: rnn.fit(X_train, y_train, epochs=10, batch_size=32,
            validation_data=(X_test, y_test))
Train on 25000 samples, validate on 5000 samples
Epoch 1/5
25000/25000 [==============================] - 299s 12ms/step - loss:
0.6574 - acc: 0.5868 - val_loss: 0.5582 - val_acc: 0.6964
Epoch 2/5
25000/25000 [==============================] - 298s 12ms/step - loss:
0.4577 - acc: 0.7786 - val_loss: 0.3546 - val_acc: 0.8448
Epoch 3/5
25000/25000 [==============================] - 296s 12ms/step - loss:
0.3277 - acc: 0.8594 - val_loss: 0.3207 - val_acc: 0.8614
Epoch 4/5
25000/25000 [==============================] - 307s 12ms/step - loss:
0.2675 - acc: 0.8864 - val_loss: 0.3056 - val_acc: 0.8700
Epoch 5/5
25000/25000 [==============================] - 310s 12ms/step - loss:
0.2217 - acc: 0.9083 - val_loss: 0.3264 - val_acc: 0.8704
[34]: <tensorflow.python.keras.callbacks.History object at 0xb3ba8828E>
```

最后，可以使用测试数据评价预测结果。`evaluate`函数返回测试损失和测试准确性。在这里，该模型准确性为85.99%：

```
[35]: results = rnn.evaluate(X_test, y_test)
20000/20000 [==============================] - 42s 2ms/step
[36]: results
[36]: [0.3415240607559681, 0.8599]
```

请注意，与CNN结果相比，该模型的准确性似乎较低，但这是一个处理起来更加困难的问题。如果在线搜索其他IMDb情感分析二分类研究，可以找到排名前80的预测结果，这里用只有三层的小型递归神经网络做得已经相当不错了，当然，我们也可以研究一些在线模型并尝试生成更好的模型。