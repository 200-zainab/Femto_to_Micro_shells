// Add the needed includes
#include <sys/stat.h>
#include <fcntl.h>
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>   
#include <errno.h>
#include <string.h>
#include <ctype.h>
#include <sys/wait.h>


#define BUF_SIZE 10000




char *strip_extra_spaces(char* str) {
  int i, x;
  for(i=x=0; str[i]; ++i)
    if(!isspace(str[i]) || (i > 0 && !isspace(str[i-1])))
      str[x++] = str[i];
  str[x] = '\0';
  return str;
}






int picoshell_main(int argc, char *argv[]) {
    // Write your code here
      char buf[BUF_SIZE];
      char buffer[BUF_SIZE];
    int last_status = 0;
    char args[BUF_SIZE];  
    int fd, fd2;  

    while (1) {
        printf("Pico shell prompt > ");
        fflush(stdout);

        if (fgets(buf, BUF_SIZE, stdin) == NULL) {
            return last_status; 
        }

        
        buf[strcspn(buf, "\n")] = 0;

        if (strncmp(buf, "echo ", 5) == 0) {
            //printf("%s\n", buf + 5);
            last_status = 0;
            printf(strip_extra_spaces(buf+5));
            printf("\n");
        }
        else if (strcmp(buf, "exit") == 0) {
            printf("Good Bye\n");
            return last_status;    
        }
else if (strncmp(buf, "cp", 2) == 0) {
    char *To_Open = NULL, *To_Open2 = NULL;
    char *token = strtok(buf, " ");
    int i = 0;

    while (token != NULL) {
        if (i == 1) {
            To_Open = token;
        } else if (i == 2) {
            To_Open2 = token;
        }
        token = strtok(NULL, " ");
        i++;
    }

    if (To_Open == NULL || To_Open2 == NULL) {
        printf("Usage: cp <src> <dest>\n");
        last_status = 1;
        continue;
    }

    fd = open(To_Open, O_RDONLY);
    if (fd == -1) {
        perror("open src");
        last_status = 1;
        continue;
    }

    fd2 = open(To_Open2, O_WRONLY | O_CREAT | O_TRUNC);
    if (fd2 == -1) {
        perror("open dest");
        close(fd);
        last_status = 1;
        continue;
    }

    ssize_t numRead;
    while ((numRead = read(fd, buffer, BUF_SIZE)) > 0) {
        if (write(fd2, buffer, numRead) != numRead) {
            perror("write");
            break;
        }
    }
    if (numRead == -1) {
        perror("read");
    }

    close(fd);
    close(fd2);
    last_status = 0;
}

else if (strncmp(buf, "cd", 2) == 0) {
     char *token = strtok(buf, " ");
    int i = 0;
   char *To_cd = NULL;
    while (token != NULL) {
        if (i == 1) {
            To_cd = token;
        }
         token = strtok(NULL, " ");
        i = i+1;    
}
       if(chdir(To_cd) == 0) {
      // printf("%s\n", To_cd);

       continue;
       }
       else
       {
        printf("cd: %s: No such file or directory\n", To_cd);
        last_status = -1;
       }
    }


else if (strncmp(buf, "pwd", 3) == 0){
    if (getcwd(buffer, BUF_SIZE) == NULL) {
        perror("getcwd failed");
        return 1;
        } else {
            printf("%s", buffer);
            printf("\n");
             } }

        else if (strlen(buf) == 0) {
            last_status = 0; 
            continue;
        }
else {
    int argc2 = 0;
    char **newargv = NULL;
    int parse_error = 0;

    char *token = strtok(buf, " \t");
    while (token != NULL) {
        char **tmp = (char **) realloc(newargv, sizeof(char *) * (argc2 + 2));
        if (!tmp) {
            parse_error = 1;
            break;
        }
        newargv = tmp;

        newargv[argc2] = strdup(token);
        if (!newargv[argc2]) {
            parse_error = 1;
            break;
        }

        argc2++;
        token = strtok(NULL, " \t");
    }

    if (parse_error || argc2 == 0) {
        if (newargv) {
            for (int i = 0; i < argc2; i++) free(newargv[i]);
            free(newargv);
        }
        last_status = 1;
        continue;  /* go back to shell prompt */
    }

    newargv[argc2] = NULL;  /* execvp requires NULL-terminated argv */

    pid_t pid = fork();
    if (pid > 0) {
        int status;
        waitpid(pid, &status, 0);
        if (WIFEXITED(status)) {
            last_status = WEXITSTATUS(status);
        } else if (WIFSIGNALED(status)) {
            last_status = 128 + WTERMSIG(status);
        } else {
            last_status = -1;
        }
    } else if (pid == 0) {
    execvp(newargv[0], newargv);

    fprintf(stderr, "%s: command not found\n", newargv[0]);
    _exit(127);   
     } else {
        perror("fork failed");
        last_status = 1;
    }

    for (int i = 0; i < argc2; i++) {
        free(newargv[i]);
    }
    free(newargv);
}

    }


    return last_status;

    
}
