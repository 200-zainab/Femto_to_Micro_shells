#include <stdio.h>
#include <string.h>
#include <stdlib.h>

#define BUF_SIZE 1000000

int femtoshell_main(int argc, char *argv[]) {
    char buf[BUF_SIZE];
    int last_status = 0;  

    while (1) {
        printf("Femto shell prompt > ");
        fflush(stdout);

        if (fgets(buf, BUF_SIZE, stdin) == NULL) {
            return last_status;  
        }

       
        buf[strcspn(buf, "\n")] = 0;

        if (strncmp(buf, "echo ", 5) == 0) {
            printf("%s\n", buf + 5);
            last_status = 0;
        } 
        else if (strcmp(buf, "exit") == 0) {
            printf("Good Bye\n");
            return last_status;   
        }
        else if (strlen(buf) == 0) {
            last_status = 0; 
            continue;
        }
        else {
            printf("Invalid command\n");
            last_status = 1;   
           
        }
    }

    return last_status;
}
