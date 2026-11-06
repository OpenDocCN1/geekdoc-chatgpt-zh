```
```python
clf_logreg=clf_logreg_GridSearch.best_estimator_
from sklearn.metrics import auc
from sklearn.metrics import roc_curve

y_pred_test_prob=clf_logreg.predict_proba(X_test)[:, 1]
fpr, tpr, thresholds=roc_curve(y_test,y_pred_test_prob)
roc_auc=auc(fpr,tpr)
roc_auc #最优模型的 AUC 为 0.92

#绘制 ROC 曲线
plt.figure(figsize=(14,7))
plt.title('Receiver Operating Characteristic')
plt.plot(fpr, tpr, 'b',label='AUC=%0.5f'%roc_auc)
plt.legend(loc='lower right')
plt.plot([0,1],[0,1],'r--')
plt.xlim([-0.1,1.0])
plt.ylim([-0.1,1.01])
plt.ylabel('True Positive Rate')
plt.xlabel('False Positive Rate')
plt.savefig('Receiver Operating Characteristic', bbox_inches='
'tight')
plt.show()
#绘制最优模型的 ROC 曲线,如图 7.11 所示
```
```

![](bbox=[0.07, 0.58, 0.9, 0.88])

图 7.11 绘制最优模型的 ROC 曲线

很多金融机构并非按照默认的违约临界点来判定是否违约,或作为是否投