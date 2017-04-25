# Project6

/* Stephen Hunsberger */
/* Shunsber */
/* 113505997 */

#include <stdio.h>
#include <sys/wait.h>
#include <sysexits.h> 
#include <err.h>
#include <unistd.h> 
#include <sys/types.h>
#include <string.h>
#include <stdlib.h>
#include "command.h"
#include "executor.h"

static void print_tree(struct tree *t);

/* want to go to the bottom left of the tree, execute the command and then*/
/* go to the next lowest level node to the right */
/* need to keep track if something returns null to kill any AND above it */

/* start with just the NONE conjunction */
int execute(struct tree *t) {

  /*NONE conjunction, is actual function to perform.*/
  /*Argv holds the command to perform*/
  
  if(t->conjunction == 0){

    /*exiting */
    if(strcmp(t->argv[0], "exit") == 0){
      exit(0);
    }
    
    /* changing directories */
    if(strcmp(t->argv[0], "cd") == 0){
      /* check if a directory is given, if not go to home dir */
      if(t->argv[1] != NULL){
	if(chdir(t->argv[1]) != 0){
	  perror("couldnt change dir"); 
	}
      }
      /* go to home directory*/
      else{
	if(chdir(getenv("HOME")) != 0){
	  perror("couldnt change to home dir"); 
	}
      }
    }
    /*  For other commands, you must create a child 
	(via fork()) and use execvp) to execute the command.*/
    else{
      switch(fork()){
        case -1:
	   perror("fork error\n");
           break;
        case 0:
	  execvp(t->argv[0], t->argv);
	  /* if it returns it must have failed */
	  printf("Failed to execute %s\n", t->argv[0]); 
	  exit(EX_OSERR); 
        default:
	  wait(NULL);
	  break;
      }
    }
  }
  
  /*AND conjunction, keep searching and needs to */
  /*figure something else out for it*/
  else if(t->conjunction == 1){
  }
  
  /*PIPE conjunction, keep searching and needs to */
  /*figure something else out for it*/
  else if(t->conjunction == 4){
  }
  
  /*SUB SHELL conjunction, keep searching and needs to */
  /*figure something else out for it*/
  else if(t->conjunction == 5){
  }


   /* print_tree(t); */ 

   return 0;
}

static void print_tree(struct tree *t) {
   if (t != NULL) {
      print_tree(t->left);

      if (t->conjunction == NONE) {
         printf("NONE: %s, ", t->argv[0]);
      } else {
         printf("%s, ", conj[t->conjunction]);
      }
      printf("IR: %s, ", t->input);
      printf("OR: %s\n", t->output);

      print_tree(t->right);
      
    }
}
