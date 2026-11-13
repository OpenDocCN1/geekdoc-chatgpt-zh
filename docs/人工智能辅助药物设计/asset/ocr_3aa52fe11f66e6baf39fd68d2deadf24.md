```
Entropy(D(Gender)^{2}) = -\left(\frac{4}{7} \ln \frac{4}{7} + \frac{3}{7} \ln \frac{3}{7}\right) = 0.682 9
```

以“血压”属性作为划分依据所带来的信息熵为

```
Entropy(D(BP)^{1}) = -\left(\frac{7}{14} \ln \frac{7}{14} + \frac{7}{14} \ln \frac{7}{14}\right) = 0.693 1
Entropy(D(BP)^{2}) = -\left(\frac{1}{2} \ln \frac{1}{2} + \frac{1}{2} \ln \frac{1}{2}\right) = 0.693 1
Entropy(D(BP)^{3}) = -\left(\frac{1}{1} \ln \frac{1}{1} + \frac{0}{1} \ln \frac{0}{1}\right) = 0.0
```

以“胆固醇”属性作为划分依据所带来的信息熵为

```
Entropy(D(Chol)^{1}) = -\left(\frac{5}{10} \ln \frac{5}{10} + \frac{5}{10} \ln \frac{5}{10}\right) = 0.693 1
Entropy(D(Chol)^{2}) = -\left(\frac{4}{6} \ln \frac{4}{6} + \frac{2}{6} \ln \frac{2}{6}\right) = 0.636 5
Entropy(D(Chol)^{3}) = -\left(\frac{0}{1} \ln \frac{0}{1} + \frac{1}{1} \ln \frac{1}{1}\right) = 0.0
```

以“钠钾比”属性作为划分依据所带来的信息熵为

```
Entropy(D(NaK)^{1}) = -\left(\frac{2}{2} \ln \frac{2}{2} + \frac{0}{2} \ln \frac{0}{2}\right) = 0.0
Entropy(D(NaK)^{2}) = -\left(\frac{7}{15} \ln \frac{7}{15} + \frac{8}{15} \ln \frac{8}{15}\right) = 0.690 9
```

综上所述，我们以“年龄”“性别”“血压”“胆固醇”和“钠钾比”这5个属性计算出了信息熵。可以看到，以“年龄”为属性的信息熵最低。