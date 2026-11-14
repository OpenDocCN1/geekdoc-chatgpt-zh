```
if(code.type() = = image.EAN5) :
    return "EAN5"
if(code.type() = = image.EAN8) :
    return "EAN8"
if(code.type() = = image.UPCE) :
    return "UPCE"
if(code.type() = = image.ISBN10) :
    return "ISBN10"
if(code.type() = = image.UPCA) :
    return "UPCA"
if(code.type() = = image.EAN13) :
    return "EAN13"
if(code.type() = = image.ISBN13) :
    return "ISBN13"
if(code.type() = = image.I25) :
    return "I25"
if(code.type() = = image.DATABAR) :
    return "DATABAR"
if(code.type() = = image.DATABAR_EXP) :
    return "DATABAR_EXP"
if(code.type() = = image.CODABAR) :
    return "CODABAR"
if(code.type() = = image.CODE39) :
    return "CODE39"
if(code.type() = = image.PDF417) :
    return "PDF417"
if(code.type() = = image.CODE93) :
    return "CODE93"
if(code.type() = = image.CODE128) :
    return "CODE128"
```

# 5）条码识别设计

板上 Python 库中提供 image.find_barcodes([roi]) 方法，用来查找 roi 内所有一维条形码并返回一个 image.barcode 对象列表。请参考 image.barcode