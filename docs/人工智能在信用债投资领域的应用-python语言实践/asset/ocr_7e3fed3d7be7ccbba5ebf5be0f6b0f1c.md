```
model2.add(Activation('softmax'))
# (4) 递归神经网络 (Recurrent Neural Network,RNN)
from keras.layers import Embedding,LSTM
model3.add(Embedding(20000,128))
model3.add(LSTM(128,dropout=0.2,recurrent_dropout=0.2))
model3.add(Dense(1,activation='sigmoid'))
```

# 4) 检查模型(Inspect Model)

```
model.output_shape Model output shape
model.summary() Model summary representation
model.get_config() Model configuration
model.get_weights() List all weight tensors in the model
```

# 5) 编译模型(Compile Model)

```
#MLP: Binary Classification
model.compile(optimizer='adam',loss='binary_crossentropy',metrics=['accuracy'])
#MLP: Multi-Class Classification
model.compile(optimizer='rmsprop',loss='categorical_crossentropy',metrics=['accuracy'])
#MLP:Regression
model.compile(optimizer='rmsprop',loss='mse',metrics=['mae'])
#Recurrent Neural Network
model3.compile(loss='binary_crossentropy',optimizer='adam',metrics=['accuracy'])
```

# 6) 训练模型(Model Training)

```
model3.fit(x_train4,y_train4,batch_size=32,epochs=15,verbose=1,validation_data=(x_test4,y_test4))
```

# 7) 模型评估

```
score=model3.evaluate(x_test,y_test,batch_size=32)
```

# 8) 预测

```
model3.predict(x_test4, batch_size=32)
model3.predict_classes(x_test4,batch_size=32)
```

# 9) 保存和重新加载模型

```
from keras.models import load_model
model3.save('model_file.h5')
my_model=load_model('my_model.h5')
```