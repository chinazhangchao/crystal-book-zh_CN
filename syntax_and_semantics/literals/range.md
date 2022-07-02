# 范围(Range)

[范围](http://crystal-lang.org/api/Range.html) 通常以范围字面量构建:

```crystal
x..y  # 闭区间, 数学表示为: [x, y]
x...y # 左闭右开区间, 数学表示为: [x, y)
```

这样好记一些：多出来的一个点就像把*y*推得更远，这样它就在范围外面了。
