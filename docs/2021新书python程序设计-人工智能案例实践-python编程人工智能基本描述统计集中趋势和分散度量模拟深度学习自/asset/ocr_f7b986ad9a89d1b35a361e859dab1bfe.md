```
In [11]: deck_of_cards.shuffle()

In [12]: for axes in axes_list.ravel():
....:     axes.get_xaxis().set_visible(False)
....:     axes.get_yaxis().set_visible(False)
....:     image_name = deck_of_cards.deal_card().image_name
....:     img = mpimg.imread(str(path.joinpath(image_name).resolve()))
....:     axes.imshow(img)
```

![](bbox=[0.205, 0.215, 0.795, 0.455])