```
XnonDefault=df.loc[df["isDefault"]==0]
correlationNonDefault=XnonDefault.loc[:, df.columns != 'isDefault'].corr()
mask=np.zeros_like(correlationNonDefault)
indices=np.triu_indices_from(correlationNonDefault)
mask[indices]=True
grid_kws={"width_ratios": (.9, .9, .05), "wspace": 0.2}
f, (ax1, ax2, cbar_ax) = plt.subplots(1, 3, gridspec_kw=grid_kws, figsize=(14, 9))
cmap=sns.diverging_palette(220, 8, as_cmap=True)
ax1=sns.heatmap(correlationNonDefault, ax=ax1, vmin=-1, vmax=1, cmap=cmap, square=False, linewidths=0.5, mask=mask, cbar=False)
ax1.set_xticklabels(ax1.get_xticklabels(), size=16)
ax1.set_yticklabels(ax1.get_yticklabels(), size=16)
ax1.set_title('Non Default', size=20)
correlationDefault=XDefault.loc[:, df.columns != 'isDefault'].corr()
ax2=sns.heatmap(correlationDefault, vmin=-1, vmax=1, cmap=cmap, ax=ax2, square=False, linewidths=0.5, mask=mask, yticklabels=False, cbar_ax=cbar_ax,cbar_kws={'orientation': 'vertical','ticks': [-1, -0.5, 0, 0.5, 1]})
ax2.set_xticklabels(ax2.get_xticklabels(), size=16)
ax2.set_title('Default', size=20)
cbar_ax.set_yticklabels(cbar_ax.get_yticklabels(), size=14)
plt.savefig('Feature Correlation', bbox_inches='tight') # 输出指标的相关性图谱, 如图 7.3 所示
```

# Non Default

# Default

![](bbox=[0.1, 0.6, 0.92, 0.9])

图 7.3 不同目标变量的指标相关性图谱