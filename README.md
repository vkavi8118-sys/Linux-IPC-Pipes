# Linux-IPC--Pipes
Linux-IPC-Pipes


# Ex03-Linux IPC - Pipes

# AIM:
To write a C program that illustrate communication between two process using unnamed and named pipes

# DESIGN STEPS:

### Step 1:

Navigate to any Linux environment installed on the system or installed inside a virtual environment like virtual box/vmware or online linux JSLinux (https://bellard.org/jslinux/vm.html?url=alpine-x86.cfg&mem=192) or docker.

### Step 2:

Write the C Program using Linux Process API - pipe(), fifo()

### Step 3:

Testing the C Program for the desired output. 

# PROGRAM:

## C Program that illustrate communication between two process using unnamed pipes using Linux API system calls
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <string.h>
#include <fcntl.h>
#include <unistd.h>
#include <sys/wait.h>

void server(int, int);
void client(int, int);

int main()
{
    int p1[2], p2[2], pid;

    pipe(p1);
    pipe(p2);

    pid = fork();

    if(pid == 0)
    {
        close(p1[1]);
        close(p2[0]);

        server(p1[0], p2[1]);

        exit(0);
    }

    close(p1[0]);
    close(p2[1]);

    client(p1[1], p2[0]);

    wait(NULL);

    return 0;
}

void server(int rfd, int wfd)
{
    int n;
    char fname[2000];
    char buff[2000];

    n = read(rfd, fname, 2000);

    fname[n] = '\0';

    int fd = open(fname, O_RDONLY);

    if(fd < 0)
    {
        write(wfd, "File not found", 14);
    }
    else
    {
        n = read(fd, buff, 2000);

        write(wfd, buff, n);

        close(fd);
    }
}

void client(int wfd, int rfd)
{
    int n;
    char fname[2000];
    char buff[2000];

    printf("Enter filename: ");
    scanf("%s", fname);

    write(wfd, fname, 2000);

    n = read(rfd, buff, 2000);

    buff[n] = '\0';

    printf("\nFile Content:\n%s\n", buff);
}




## OUTPUT
<img width="521" height="443" alt="VirtualBox_Parrot Security 6 0_15_05_2026_10_30_21" src="https://github.com/user-attachments/assets/b857bebd-ff6c-48a8-b2ca-62436de49439" />


## C Program that illustrate communication between two process using named pipes using Linux API system calls

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <string.h>

#define FIFO_FILE "/tmp/my_fifo"
#define FILE_NAME "hello.txt"

void server();
void client();

int main()
{
    pid_t pid;

    mkfifo(FIFO_FILE, 0666);

    pid = fork();

    if(pid > 0)
    {
        sleep(1);

        server();
    }
    else if(pid == 0)
    {
        client();
    }
    else
    {
        printf("Fork failed\n");
        exit(EXIT_FAILURE);
    }

    return 0;
}

void server()
{
    int fifo_fd, file_fd;
    char buffer[1024];
    ssize_t bytes_read;

    file_fd = open(FILE_NAME, O_RDONLY);

    if(file_fd == -1)
    {
        printf("Error opening file\n");
        exit(EXIT_FAILURE);
    }

    fifo_fd = open(FIFO_FILE, O_WRONLY);

    if(fifo_fd == -1)
    {
        printf("Error opening FIFO\n");
        exit(EXIT_FAILURE);
    }

    while((bytes_read = read(file_fd, buffer, sizeof(buffer))) > 0)
    {
        write(fifo_fd, buffer, bytes_read);
    }

    close(file_fd);
    close(fifo_fd);
}

void client()
{
    int fifo_fd;
    char buffer[1024];
    ssize_t bytes_read;

    fifo_fd = open(FIFO_FILE, O_RDONLY);

    if(fifo_fd == -1)
    {
        printf("Error opening FIFO\n");
        exit(EXIT_FAILURE);
    }

    while((bytes_read = read(fifo_fd, buffer, sizeof(buffer))) > 0)
    {
        write(STDOUT_FILENO, buffer, bytes_read);
    }

    close(fifo_fd);
}



## OUTPUT
<img width="564" height="387" alt="VirtualBox_Parrot Security 6 0_15_05_2026_10_34_04" src="https://github.com/user-attachments/assets/703fee4c-461f-4968-bd43-bdc2e66d933d" />


# RESULT:
The program is executed successfully.
