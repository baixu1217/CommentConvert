# CommentConvert
//"CommentConvert.h"

#ifndef __COMMENTCONVERT_H__
#define __COMMENTCONVERT_H__

#include<stdlib.h>
#include<stdio.h>

typedef enum CONVERT_STATE
{
 NULL_STATE,
 C_STATE,
 CPP_STATE,
 END_STATE
}StateType;

#define INPUTFILENAME "input.c.txt"
#define OUTPUTFILENAME "output.c.txt"

void CommentConvert();
void ConvertWork(FILE* read,FILE* write);
void DoCState(FILE* read,FILE* write);
void DoCppState(FILE* read,FILE* write);
void DoNullState(FILE* read,FILE* write);
#endif // __COMMENTCONVERT_H__




//"CommentConvert.c"
#include"CommentConvert.h"

StateType state;

void ConvertWork(FILE *read, FILE *write)
{
 state = NULL_STATE;
 while(state != END_STATE)
 {
  switch(state)
  {
  case NULL_STATE:
   DoNullState(read,write);
   break;
  case C_STATE:
   DoCState(read,write);
   break;
  case CPP_STATE:
   DoCppState(read,write);
   break;
  }
 }
}


void DoCState(FILE* read,FILE* write)
{
 int first = fgetc(read);
 int second = 0;
 int third = 0;
 switch(first)
 {
 case '*':
  second = fgetc(read);
  if(second == '/')
  {
   third = fgetc(read);
   if(third == '\n')
   {
    ungetc(third,read);
   }
   else
   {
    ungetc(third,read);
    fputc('\n',write);
   }
   state = NULL_STATE;
  }
  else if(second == '*')
  {
   int third = 0;
   third = fgetc(read);
   if(third == '/')
   {
    fputc(first,write);
    state = NULL_STATE;
   }
   else
   {
    fputc(first,write);
    fputc(second,write);
   }
  }
  else
  {
   fputc(first,write);
   fputc(second,write);
  }
  break;
 case '\n':
  fputc(first,write);
  fputc('/',write);
  fputc('/',write);
  state = NULL_STATE;
  break;
 case EOF:
  fputc(first,write);
  state = END_STATE;
  break;
 default:
  fputc(first,write);
  break;
 }
}
void DoCppState(FILE* read,FILE* write)
{
 int first = 0;
 first = fgetc(read);
 switch(first)
 {
 case '\n':
  fputc(first,write);
  state = NULL_STATE;
  break;
 case EOF:
  state = END_STATE;
  break;
 default:
  fputc(first,write);
  break;
 }
}
void DoNullState(FILE* read,FILE* write)
{
 int first = fgetc(read);
 int second = 0;
 switch(first)
 {
 case '/':
  second = fgetc(read);
  if(second == '*')
  {
   fputc(first,write);
   fputc('/',write);
   state = C_STATE;
  }
  else if(second == '/')
  {
   fputc(first,write);
   fputc(second,write);
   state = CPP_STATE;
  }
  else
  {
   fputc(first,write);
   fputc(second,write);
  }
  break;
 case EOF:
  fputc(first,write);
  state = END_STATE;
  break;
 default:
  fputc(first,write);
  break;
 }
}
void CommentConvert()
{
 FILE *pRead = fopen(INPUTFILENAME,"r");
 FILE *pWrite = NULL;
 if(pRead == NULL)
 {
  perror("open file  for read:");
  exit(EXIT_FAILURE);
 }
 pWrite =  fopen(OUTPUTFILENAME,"w");
 if(pWrite == NULL)
 {
  fclose(pRead);
  perror("open file for write:");
  exit(EXIT_FAILURE);
 }
 
 ConvertWork(pRead,pWrite);
 
 fclose(pRead);
 fclose(pWrite);

 
}

//"test.c"

#include<stdio.h>

void test()
{
 CommentConvert();
}
int main()
{
 test();
 return 0;
}

//"input.c"

// 1.一般情况
// int i = 0;

// 2.换行问题
// int i = 0;
int j = 0;
// int i = 0;
int j = 0;

// 3.匹配问题
//int i = 0;/*xxxxx

// 4.多行注释问题
//
//int i=0;
int j = 0;
int k = 0;
*/int k = 0;

// 5.连续注释问题
//
//

// 6.连续的**/问题
//*

// 7.C++注释问题
// /*xxxxxxxxxxxx*/

// 8.C注释本身不匹配
// int i = 0;
//


//转换后

// 1.一般情况
/* int i = 0; */

// 2.换行问题
/* int i = 0; */int j = 0;
/* int i = 0; */
int j = 0;

// 3.匹配问题
/*int i = 0;/*xxxxx*/

// 4.多行注释问题
/*
int i=0;
int j = 0;
int k = 0;
*/int k = 0;

// 5.连续注释问题
/**//**/

// 6.连续的**/问题
/***/

// 7.C++注释问题
// /*xxxxxxxxxxxx*/

// 8.C注释本身不匹配
/* int i = 0;










