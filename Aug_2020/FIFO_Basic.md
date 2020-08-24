# FIFO基础 8-24

- FIFO就是先入先出队列。包含两个基本操作，插入一个新的项和取出一个最早插入的项。
- FIFO的元素包括：一个head指针，一个tail指针，最大容量，当前容量，数据空间指针。
- 插入一个元素：首先判断FIFO是否满（当前容量是否等于最大容量），如果是，不允许插入。如果FIFO未满，在tail指针处插入一个元素，tail指针循环加1`(head+1) % max_size`，当前容量自增1`cur_size++`。
- 取出一个元素：首先判断FIFO是否空（当前容量是否等于0），如果是，不允许取出。如果FIFO非空，从head指针处取出一个元素，head指针循环加1，当前容量自减1。
- 注意：需要给队列加锁，防止出现写入数据还未完成就开始读数据的情况。这种情况下，插入元素需要先`try_lock`，成功后再进行插入操作，完成插入操作后解锁`unlock`。同理，取出元素也需要先`try_lock`，成功后进行取出操作，再`unlock`。

## Buffer的FIFO实现

- Buffer的元素：一个head指针`int head`，一个tail指针`int tail`，最大容量`int max_size`，当前容量`int cur_size`，数据空间指针`uint8_t * data`。

- 插入一段数据`put(uint8_t *buf,int buf_len)`

  - 判断Buffer是否满，如果是，`usleep`直到Buffer未满。
  - 判断`cur_size + buf_len > max_size`如果是，说明插入数据太多，不允许插入。可以`usleep`循环直到判断否。
  - `try_lock`，可以使用`while`和`usleep`的组合直到成功lock。
  - 判断`tail+buf_len>max_size`如果是，说明数据需要分两段插入。首先`memcpy(data+tail,buf,max_size-tail)`，其次`memcpy(data,buf+max_size-tail,buf_len-(max_size-tail))`。完成数据拷贝工作。令`tail=buf_len-(max_size-tail)`, `cur_size+=buf_len`. 如果判断否，说明数据可以直接插入：`memcpy(data+tail,buf,buf_len)`。然后令`tail+=buflen`和`cur_size+=buf_len`
  - `unlock`解锁

- 取出一段数据`get(uint8_t *buf,int buf_len)`

  - 判断Buffer是否空，如果是，`usleep`直到Buffer非空
  - 判断`cur_size < buf_len`如果是，则说明取出的数据太多，不允许取出。可以`usleep`循环直到判断否
  - `try_lock`，可以使用`while`和`usleep`的组合直到成功lock。
  - 判断`head+buf_len>max_size`如果是，说明数据需要分两段取出。首先`memcpy(buf,data+head,max_size-head)`，其次`memcpy(buf,data,head+buf_len-max_size)`. 令`head = head+buf_len-max_size`, `cur_size -= buf_len`. 如果判断否，说明数据可以一次性取出`memcpy(buf,data+head,buf_len)`, 令`head+=buf_len`, `cur_size-=buf_len`.

- 判断空`is_empty()`

  - `if(cur_size == 0)`

- 判断满`is_full()`

  - `if(cur_size == max_size)`

- `init(int max_size)`

  - ```c++
    this->max_size = max_size;
    this->data = new uint8_t [max_size];
    this->head = 0;
    this->tail = 0;
    this_cur_size = 0;
    ```

- `destory()`

  - ```c++
    if(this->cur_size>0) fprintf(stderr, "BUFFER IS NO EMPTY\n");
    delete this->data;
    ```