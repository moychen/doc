```c
/*
 * This structure is stored inside the "private_data" member of the file
 * structure and rapresent the main data sructure for the eventpoll
 * interface.
 */
struct eventpoll {
	/* Protect the this structure access */
	spinlock_t lock;
	/*
	 * This mutex is used to ensure that files are not removed
	 * while epoll is using them. This is held during the event
	 * collection loop, the file cleanup path, the epoll file exit
	 * code and the ctl operations.
	 */
	struct mutex mtx;
	/* Wait queue used by sys_epoll_wait() */
	wait_queue_head_t wq; // 等待队列
	/* Wait queue used by file->poll() */
	wait_queue_head_t poll_wait;
	/* List of ready file descriptors */
	struct list_head rdllist;
	/* RB tree root used to store monitored fd structs */
	struct rb_root rbr;
	/*
	 * This is a single linked list that chains all the "struct epitem" that
	 * happened while transfering ready events to userspace w/out
	 * holding ->lock.
	 */
	struct epitem *ovflist; // 就绪事件链表
	struct user_struct *user; /* The user that created the eventpoll descriptor */
};
```

