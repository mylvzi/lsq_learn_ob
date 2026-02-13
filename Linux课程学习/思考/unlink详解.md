>unlink只是删除一个**名字**, 删除的只是dentry中文件名 和 inode 之间的映射关系
>也就是说, unlink只会让 一个文件的硬链接计数-1


![[Pasted image 20251210121611.png]]


>unlink的作用 : 
>这涉及到Linux设计的哲学, 文件名只是一个入口, 并不是文件本体


实现匿名文件
```css
1.open()
2.unlink()
3.write()
```

![[Pasted image 20251210142607.png]]


```c++
#include <iostream>

#include <sys/types.h>

#include <sys/stat.h>

#include <fcntl.h>

#include <string>

#include <unistd.h>

#include <stdio.h>

  

int main()

{

    std::string filename = "log.txt";

    int fd = open(filename.c_str(), O_CREAT | O_WRONLY | O_TRUNC, 0644);

    if(fd < 0)

    {

        perror("open");

        return 1;

    }

  

    std::cout << "File open : fd = " << fd << ", pid = " << getpid() <<std::endl;

    std::cout << "Now Unlink the file" << std::endl;

  

    unlink(filename.c_str());

  

    std::cout << "Unlinked the file : " << filename << std::endl;

  

    while(true)

    {

        sleep(1);

        std::string msg = "hello linux";

        write(fd, msg.c_str(), msg.size());

    }

    return 0;

}
```


>匿名文件就不是用来永久化保存数据的, 而是用来在进程运行期间安全可靠的存储临时数据, 进程退出后会自动回收数据