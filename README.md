# Linux-IPC-Shared-memory
Ex06-Linux IPC-Shared-memory

# AIM:
To Write a C program that illustrates two processes communicating using shared memory.

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - Shared Memory

### Step 3:

Execute the C Program for the desired output. 

# PROGRAM:

## Write a C program that illustrates two processes communicating using shared memory.

```
// shm.c

#include <unistd.h> 
#include <stdlib.h> 
#include <stdio.h> 
#include <string.h>
#include <sys/shm.h>
#include <sys/ipc.h> // Include for ftok()
#define TEXT_SZ 2048 

struct shared_use_st
{
    int written_by_you;
    char some_text[TEXT_SZ];
};

int main()
{
    int running = 1;
    void *shared_memory = (void *)0; 
    struct shared_use_st *shared_stuff; 
    char buffer[BUFSIZ];
    int shmid;
    key_t key = ftok(".", 'R'); // Generate key
    if (key == -1)
    {
        perror("ftok");
        exit(EXIT_FAILURE);
    }
    shmid = shmget(key, sizeof(struct shared_use_st), 0666 | IPC_CREAT);
    printf("Shared memory id = %d \n", shmid);
    if (shmid == -1) {
        fprintf(stderr, "shmget failed\n"); 
        exit(EXIT_FAILURE);
    }
    shared_memory = shmat(shmid, (void *)0, 0);
    if (shared_memory == (void *)-1) {
        fprintf(stderr, "shmat failed\n"); 
        exit(EXIT_FAILURE);
    }
    printf("Memory Attached at %p\n", shared_memory); 
    shared_stuff = (struct shared_use_st *)shared_memory; 
    while (running) {
        while (shared_stuff->written_by_you == 1) {
            sleep(1);
            printf("waiting for client.\n");
        }
        printf("Enter Some Text: "); 
        fgets(buffer, BUFSIZ, stdin);
        strncpy(shared_stuff->some_text, buffer, TEXT_SZ);
        shared_stuff->written_by_you = 1;
        if (strncmp(buffer, "end", 3) == 0) {
            running = 0;
        }
        // Reset the flag after reading the input
        shared_stuff->written_by_you = 0;
    }
    if (shmdt(shared_memory) == -1) {
        fprintf(stderr, "shmdt failed\n"); 
        exit(EXIT_FAILURE);
    }       
    exit(EXIT_SUCCESS);
}
```



## OUTPUT

```
Shared memort id = 32794 
Memory Attached at 4bb22000
Enter Some Text: Hello
waiting for client.	
Enter Some Text: World
waiting for client.	
waiting for client.	
waiting for client.	
Enter Some Text: end
```

![image](https://github.com/KAVIYADHARANI/Linux-IPC-Shared-memory/assets/144870680/bf0dd9ac-da74-4804-b369-9b9ac47669db)

# RESULT:
The program is executed successfully.
