>Git中所有的数据都以**对象**的形式进行存储,主要有四种

| 对象类型       | 说明                                         |
| ---------- | ------------------------------------------ |
| **blob**   | 文件内容（binary large object），存储文件的实际数据，不包含文件名 |
| **tree**   | 目录结构（记录文件名与对应 blob 的映射）                    |
| **commit** | 一次提交的快照信息（指向一个 tree，并记录作者、时间、父提交）          |
| **tag**    | 标签对象（对 commit 的引用，并附加描述信息）                 |
# 对象的存储
当你往Git仓库中添加文件并提交
```bash
echo "hello git" > a.txt
git add .
git commit -m "first commit"
```
Git会依次生成以下对象
`git add`  :  生成 `blob`对象    存放此次修改的文件内容
`git commit`  : 生成`tree`对象(目录对象)    记录文件名`a.txt`与对应的blob对象
`git commit` : 生成`commit`对象(提交对象)    指向上面的tree,包含作者,时间,提交信息

>注 : git中生成的所有对象都会被存储到.git/objects目录下
# 示例
`commit对象` : HEAD, 指向某一次具体提交的目录
![[Pasted image 20251012203034.png]]
![[Pasted image 20251012203156.png]]

`tree对象` : HEAD所指向的数据, 本质上是一个索引(某一个分支上, 表示目录结构, 记录文件名和 对应的blob对象)
![[Pasted image 20251012203312.png]]
![[Pasted image 20251012203412.png]]

`blob对象` : 记录某次提交的具体内容
![[Pasted image 20251012203457.png]]

```bash
git cat-file -t xxx  # 查看对象类型
git cat-file -p xxx  # 查看对象详细信息
```

# 补充
在 Git 中，每一个对象（blob、tree、commit、tag）都有一个唯一的 **哈希值（hash）**，  
这个哈希值就是：

> ✅ **对象内容的加密摘要（Digest）**，  
> 由 **SHA-1** 或 **SHA-256** 算法计算而得。
> ![[Pasted image 20251012203957.png]]


>blob(binary large object)二进制大对象
>blob对象中存储的是文件的**原始数据**, 而二进制也表示数据存储的底层,所以这么起名