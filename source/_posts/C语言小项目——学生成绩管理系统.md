---
title: "C语言小项目——学生成绩管理系统"
date: 2022-03-03 22:35:44
---
## stduent.h

```
#ifndef STUDENT_H_INCLUDED
#define STUDENT_H_INCLUDED
#pragma once
#pragma warning(disable:4996)//visual studio 支持c语言必备内容
#define FILE_DATA_PATH "E:\\STUDENT.txt"//定义存放文件路径为STUDENT.txt文件
#include <stdio.h>
#include <stdlib.h>
#include  <string.h>
#include <conio.h>
#define MAX 300//最大录入信息300人
//学生信息结构体
struct isStudent
{
char name[9]; //姓名
int number; // 默认学号8位
float chinese;
float math;
float english; //定义三科成绩；
float average; //定义平均成绩
char password[20];//定义个人密码

};

typedef struct isStudent isStudent;
static isStudent temporary[1];
isStudent stu[MAX];//学生数量结构体
int nowStudentNumber;//全局变量，代表当前学生人数

//显示欢迎信息
void welcome();

//系统界面
void memu();

//学生系统界面
void studentInterface(int studentnumber13);

//管理员学生界面
void adminInterface();

//管理员增加学生信息
void creatAccount();

//删除学生信息
void deleteAccount();

//退出系统
void exitSystem();

//排序学生
void sort();

//按总成绩排序
void sortGrade();

//显示所有存储的信息
void adminShow();

//管理员查看个人信息
void studentShow();

//写入函数
void writeIn();

//查找单个信息
//void searchPersonInformation_asNumber();

//读取文件
void readOut();

//请求输入学生密码和账号
void studentInPWD();

//管理员密码登录
void adminInPWD();

//学生端查看个人信息
void studentFindShow(int studentnumber);

//学生端修改密码
void changeTheSecurityCode(int studentNumber13);

//管理员功能界面
void adminMemu();

#endif // STUDENT_H_INCLUDED
```

## main.c

```
#include <stdio.h>
#include "student.h"

int main()
{
    int income;
    welcome();//功能：欢迎界面
    getch();
    nowStudentNumber = 0;
    readOut();//读取文件
    while (1)
    {
        system("cls");
        memu();
        scanf("%d", &income);
        getchar();
        switch (income)
        {
        case 1:
            adminInPWD();//管理员端登录密码
            break;
        case 2:
            studentInPWD();//学生端登录密码
            break;
        case 3:
            printf("\t\t\t--------------------------谢谢使用！输入任意键继续--------------------------\n");
            getchar();
            return 0;
        default:
            ;
        }
    }
}
```

## stduent.c

```
/////运行支持库
#include<stdio.h>
#include "Student.h"
#pragma warning(disable:4996)//visual studio 支持c语言必备内容

//欢迎界面
void welcome()
{
    /*
    printf("\n\n\n\n\n\n\n\n");
    printf("\t\t\t\t\t*********************************\n");
    printf("\t\t\t\t\t*                               *\n");
    printf("\t\t\t\t\t*                               *\n");
    printf("\t\t\t\t\t*   欢迎进入学生成绩管理系统    *\n");
    printf("\t\t\t\t\t*                               *\n");
    printf("\t\t\t\t\t*                               *\n");
    printf("\t\t\t\t\t*********************************");
    */
printf("\n\n\n\n\n\n");
    printf("\t\t\t\t   Welcome to the Student achievement management system\n");
    printf("\t\t\t\t\t             ～ (((^^)(^^)))    \n");
    printf("\t\t\t\t\t┌────────────────────────────────────┐\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│     欢迎进入学生成绩管理系统       │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t└────────────────────────────────────┘\n");
}
//
void memu()
{
    printf("\n\n\n\n\n\n");
    printf("\t\t\t\t   Welcome to the Student achievement management system\n");
    printf("\t\t\t\t\t             ～ (((^^)(^^)))    \n");
    printf("\t\t\t\t\t┌────────────────────────────────────┐\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│      1.管理员身份登陆!             │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│      2.本科生身份登陆!             │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│      3.退出系统!                   │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t└────────────────────────────────────┘\n");
    printf("\t\t\t\t\t请输入对应的数字选择功能！");
}

void adminMemu()
{/*
        printf("\t\t\t\t\t\t-------------------------1.录入学生成绩-------------------------\n");
        printf("\n");
        printf("\t\t\t\t\t\t-------------------------2.查找学生信息-------------------------\n");
        printf("\n");
        printf("\t\t\t\t\t\t-------------------------3.删除学生信息-------------------------\n");
        printf("\n");
        printf("\t\t\t\t\t\t-------------------------4.按照总成绩排序-----------------------\n");
        printf("\n");
        printf("\t\t\t\t\t\t-------------------------5.按学号排序---------------------------\n");
        printf("\n");
        printf("\t\t\t\t\t\t-------------------------6.统计学生总数-------------------------\n");
        printf("\n");
        printf("\t\t\t\t\t\t-------------------------0.退出系统-----------------------------\n");
        */
    printf("\n\n\n\n\n\n");
    printf("\t\t\t\t   Welcome to the Student achievement management system\n");
    printf("\t\t\t\t\t             ～ (((^^)(^^)))    \n");
    printf("\t\t\t\t\t┌────────────────────┸───────────────┐\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│      1.录入学生成绩                │\n");
    printf("\t\t\t\t\t│      2.查找学生信息                │\n");
    printf("\t\t\t\t\t│      3.删除学生信息                │\n");
    printf("\t\t\t\t\t│      4.按照总成绩排序              │\n");
    printf("\t\t\t\t\t│      5.按学号排序                  │\n");
    printf("\t\t\t\t\t│      6.统计学生总数                │\n");
    printf("\t\t\t\t\t│      0.退出系统                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t└────────────────────────────────────┘\n");
    printf("\t\t\t\t\t请输入对应的数字选择功能！");
}
//写入文件
void writeIn()//写入文件
{

    char ch;

    FILE* pFile;

    pFile = fopen("STUDENT.txt", "w");//状态，覆写

    if (pFile == NULL)
    {
        printf("\t\t\t写入文件失败。\n");
        printf("\t\t\t文件保存错误。\n");
        printf("\t\t\t输入任意键退出。\n");

        getch();

        return 0;//以不正常状态退出
    }
    else
    {

        //将所有内容写入文件
        for (int i = 0; i < nowStudentNumber; i++)
        {
            fprintf(pFile, "%-9s %-10d %-6.f %-6.f %-6.f %-6.f %-22s\n", stu[i].name, stu[i].number, stu[i].chinese, stu[i].math, stu[i].english, stu[i].average, stu[i].password);
        }

        fclose(pFile);

        getchar();

    }
}

//读取文件
void readOut()//从文件读取
{
    int i = 0;

    FILE* pFile;
    pFile = fopen("STUDENT.txt", "r");//此处需要更换文件路径

    if (pFile == NULL)
    {
        printf("\t\t\t打开文件失败。\n");
        return;
    }
    else
    {
        while (fscanf(pFile, "%s %d %f %f %f %f %s\n", &stu[i].name, &stu[i].number, &stu[i].chinese, &stu[i].math, &stu[i].english, &stu[i].average, &stu[i].password)!=EOF)//读取一组数据后指针指向下一组数据，并且判断是否指向最后一行
        {
            //fscanf(pFile, "%s %d %f %f %f %f %s\n", &stu[i].name, &stu[i].number, &stu[i].chinese, &stu[i].math, &stu[i].english, &stu[i].average, &stu[i].password);
            i++;
        }
        nowStudentNumber = i;//此处为记录有多少条信息,后可改为宏变
        i--;
        fclose(pFile);                //关闭文件；
    }
}

////管理员端接入
//管理员界面
void adminInterface()//.管理员界面
{
    while (1)
    {
loop2:       system("cls");
        adminMemu();
        int ch = 9;//读取选择
        scanf("%d",&ch);
        getchar();
        switch (ch)
        {
        case 1://1. 录入学生成绩
            creatAccount();
            break;
        case 2://2.查找学生信息
            studentShow();//、
            break;
        case 3://3.删除学生信息
            deleteAccount();
            break;
        case 4://4.按总成绩排序
            sortGrade();//ChangeStudent();
            break;
        case 5://5.按照学号排序
            sort();//RankStudent();
            break;
        case 6://6.统计学生总数
            printf("\t");
            printf("\t\t\t\t当前学生总数为：%d\n",nowStudentNumber);
            printf("\t\t\t\t\t-----------按任意键返回菜单------------");
            getch();
            break;
        case 0://退出系统
            exit(0);
        default:
            ;
        }
        //getchar();
    }
    return 0;
}

//管理员密码登录

void adminInPWD()
{

loop:    system("cls");
    printf("\n\n\n\n\n\n");
    printf("\t\t\t请输入管理员账号:");
    char adminnumberin[20];
    scanf("%s", adminnumberin);
    char passwordin[20];//输入的密码
    char admin[6] = "admin";
    if (strcmp(admin, adminnumberin) == 0)
    {

loop1:        system("cls");
        printf("\n\n\n\n\n\n");
        printf("\t\t\t请输入密码:");
        scanf("%s", passwordin);
        if (strcmp(admin, passwordin) == 0)
        {
            adminInterface();
        }
        else
        {
            printf("\n\n\n\n\n\n");
            printf("\t\t\t密码输入错误！！");
            sleep(1);
        }
        goto loop1;

    }
    else{
        printf("\n\n\n\n\n\n");
        printf("\t\t\t账号输入错误！！");
        sleep(1);
        goto loop;
    }
}

//管理员添加学生信息

void creatAccount()
{
    int i = nowStudentNumber;

    system("cls");

    printf("\n\n\n\n\n\n");
    printf("\t\t\t\t\t\t请输入学生学号:");
    scanf("%d", &temporary[i].number);
    printf("\n\n\t\t\t\t\t\t请输入学生姓名:");
    scanf("%s", &temporary[i].name);
    printf("\n\n\t\t\t\t\t\t请输入语文，数学，英语三科成绩\n");
    printf("\t\t\t\t\t\t");
    scanf("%f %f %f", &temporary[i].chinese, &temporary[i].math, &temporary[i].english);
    temporary[i].average = temporary[i].chinese + temporary[i].math + temporary[i].english;

    strcpy(temporary[i].password, "123456");

    printf("\n\n\t\t\t\t\t\t学生的默认密码为123456!");

    //strcpy(stu[nowStudentNumber], temporary[0]);
    stu[i] = temporary[i];

    nowStudentNumber = i + 1;

    writeIn();
    readOut();

}

//管理员查询单个信息

void studentShow()
{
    readOut();

    int flag, s;//此处变量用来接收查询的学号
    char name[10];//此处用来接收查询的学生姓名
    int flag1 = 0;//此处变量用来无法查询到学号时返回变量
    int number;//此处接收用户个数
    system("cls");
    printf("\n\n\n\n");
    printf("\t\t\t按照学号查询，请按“1”\n");
    printf("\t\t\t按照姓名查询，请按“2”\n");
    printf("\t\t\t返回上一菜单，请按“0”\n");
    printf("\t\t\t请输入查询方式\n");//按照不同方式进行查询
    printf("\t\t\t");
    scanf("%d", &flag);
    if(0 == flag)
    return ;
    if (1 == flag)
    {
        system("cls");
        printf("\n\n\n\n");
        printf("\t\t\t--------------------------请输入要查询的学号:--------------------------\n");
        printf("\t\t\t");
        scanf("%d", &s);

        number = nowStudentNumber;
        for (int i = 0; i < number; i++)
        {
            if (stu[i].number == s)
            {
        system("cls");
                printf("\n\n\n");
                printf("\t\t\t%-9s %-9s %-6s %-6s %-6s %-6s\n", "名字", "学号", "语文", "数学", "英语", "总分");
                printf("\t\t\t-------------------------------------------------------------------\n");
                printf("\t\t\t%-9s %-9d %-6.1f %-6.1f %-6.1f %-6.1f\n", stu[i].name, stu[i].number, stu[i].chinese, stu[i].math, stu[i].english, stu[i].average);
                printf("\n\n\n");
                printf("\t\t\t-------------------------按任意键返回上一界面----------------------\n");
                getch();

                flag1 = 1;
                return;
            }

        }

        if (flag1 == 0)
        {
            printf("\n\n");
            printf("\t\t\t--------------------------查询不到当前学生信息--------------------------\n");
            printf("\t\t\t------------------------------按任意键返回------------------------------");
            getch();

        }
    }

    if (2 == flag)
    {
        printf("\t\t\t--------------------------请输入要查询的学生姓名：--------------------------\n");

        scanf("%s", name);

        number = nowStudentNumber;   //此处需补
        for (int i = 0; i < number; i++)
        {
            if (0 == strcmp(stu[i].name, name))
            {
                system("cls");
                printf("\n\n\n");
                printf("\t\t\t%-9s %-9s %-6s %-6s %-6s %-6s\n", "名字", "  学号", "语文", "数学", "英语", "总分");
                printf("\t\t\t-------------------------------------------------------------------\n");
                printf("\t\t\t%-9s %-9d %-6.1f %-6.1f %-6.1f %-6.1f\n", stu[i].name, stu[i].number, stu[i].chinese, stu[i].math, stu[i].english, stu[i].average);
                printf("\n\n\n");
                printf("\t\t\t-------------------------按任意键返回上一界面----------------------\n");
                getch();

                flag1 = 1;
                return ;
            }
        }

        if (flag1 == 0)
        {
            printf("\n\n");
            printf("\t\t\t--------------------------查询不到当前学生信息--------------------------\n");

            printf("\t\t\t------------------------------按任意键返回------------------------------");
            getch();
        }
    }
}

//管理员端按学号查询

void sort()
{
    struct isStudent MODLE;
    int i, n, j;
    n = nowStudentNumber;//此处记录传入的用户个数

    isStudent temp[MAX];
    for (int h = 0; h < n; h++)
    {
        temp[h] = stu[h];
    }
    for (i = 0; i < n; i++)
        for (j = 0; j < n - i; j++)
            if (temp[j].number < temp[j + 1].number)
            {
                MODLE = temp[j];
                temp[j] = temp[j + 1];
                temp[j + 1] = MODLE;
            }

    system("cls");
    printf("\n\n\n");
    printf("\t\t\t%-9s %-9s %-6s %-6s %-6s %-6s\n", "   名字", "  学号", "语文", "数学", "英语", "总分");
    printf("\t\t\t-------------------------------------------------------------------\n");
    printf("\n");
    for (i = 0; i < nowStudentNumber; i++)
    {
        printf("\t\t\t%-9s %-10d %-6.1f %-6.1f %-6.1f %-6.1f\n", temp[i].name, temp[i].number, temp[i].chinese, temp[i].math, temp[i].english, temp[i].average);
    }
    printf("\n");
    printf("\t\t\t--------------------------输入任意键继续--------------------------\n");

    getch();

}

//按成绩排序

void sortGrade()
{   //readOut();
    struct isStudent MODRE;
    int i, n, j;
    n = nowStudentNumber;//此处记录传入用户的个数
    isStudent temp[MAX];
    for (int h = 0; h < n; h++)
    {
        temp[h] = stu[h];
    }
    for (i = 0; i < n; i++)
        for (j = 0; j < n - i; j++)
            if (temp[j].average < temp[j + 1].average)
            {
                MODRE = temp[j];
                temp[j] = temp[j + 1];
                temp[j + 1] = MODRE;
            }

    system("cls");
    printf("\n\n\n");
    printf("\t\t\t%-9s %-9s %-6s %-6s %-6s %-6s\n", "   名字", "  学号", "语文", "数学", "英语", "总分");
    printf("\t\t\t-------------------------------------------------------------------\n");
    printf("\n");
    for (i = 0; i < nowStudentNumber; i++)
    {
        printf("\t\t\t%-9s %-10d %-6.1f %-6.1f %-6.1f %-6.1f\n", temp[i].name, temp[i].number, temp[i].chinese, temp[i].math, temp[i].english, temp[i].average);
    }
    printf("\n");
    printf("\t\t\t--------------------------输入任意键继续--------------------------\n");

    getch();
}

////学生端内容

//学生界面

void studentInterface(int studentnumber13)//这里形参代表找到的学生学号
{
    while (1)
    {
    system("cls");
            printf("\n\n\n\n\n\n");
    printf("\t\t\t\t   Welcome to the Student achievement management system\n");
    printf("\t\t\t\t\t             ～ (((^^)(^^)))    \n");
    printf("\t\t\t\t\t┌────────────────────────────────────┐\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│  1.查询个人信息                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│  2.修改账号密码                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t│  3.退出                            │\n");
    printf("\t\t\t\t\t│                                    │\n");
    printf("\t\t\t\t\t└────────────────────────────────────┘\n");
    printf("\t\t\t\t\t请输入:");

        int i;

        scanf("%d", &i);

        if (i == 1)
        {
            studentFindShow(studentnumber13);//searchPersonInformation_asNumber(student* p,len);
        }
        if (i == 2)
        {
            changeTheSecurityCode(studentnumber13);//修改账户密码
        }
        if (i == 3)
        {
            exit(0);//退出，返回上一界面（go doing）
        }
        getchar();
    }

}

//学生端密码登录

void studentInPWD()
{
//   for(int j=0;j< nowStudentNumber;j++)
//   {
//
//       printf("%s %d %f %f %f %f %s",stu[j].name,stu[j].number,stu[j].chinese,stu[j].match,stu[j].english,stu[j].average,stu[j].stu[20]);
//
//   }
loop9:  system("cls");

    printf("\n\n\n");
    printf("\t\t\t请输入学号:");

    int studentnumberin;

    scanf("%d", &studentnumberin);

    int flag12=0;//记录学生的账号是哪一个
    int flag = 0;//判断是否找到了学生账号
    char passwordin[20];//输入的密码
    readOut();

    for (int i = 0; i < nowStudentNumber; i++)
    {
        if (stu[i].number == studentnumberin)
        {
        loop5:    system("cls");

            printf("\n\n\n");
            printf("\t\t\t请输入密码:");

            scanf("%s", passwordin);

            flag12 = 1;
            if (strcmp(passwordin, stu[i].password) == 0)
            {

                flag = 1;

                studentInterface(studentnumberin);//这里将找到的学生编号传到下一界面
            }
        }

    }
            if(flag!=0)
        {
            printf("\n\n\n\n\n\n");
            printf("\t\t\t密码输入错误！！");
            sleep(1);
            goto loop5;
        }

        if(flag12!=0)
            {
            fflush(stdin);
            printf("\n\n\n\n\n\n");
            printf("\t\t\t账号输入错误！！");
            sleep(2);
            goto loop9;
            }

//            getch();
//
//            system("cls");

/*
    if (flag == 0)
    {
        printf("抱歉，查无此账号\n");
        getchar();
        getch();

        return;
    }
    */
//    getchar();
}

//学生端展示个人信息

void studentFindShow(int studentnumber)
{
    int flag, s;//此处变量用来接收查询的学号
    char name[10];//此处用来接收查询的学生姓名
    int flag1 = 0;//此处变量用来无法查询到学号时返回变量
    int number;//此处接收用户个数

        number = nowStudentNumber;
        system("cls");
        printf("\n\n\t\t\t%-9s %-9s %-6s %-6s %-6s %-6s\n", "名字", "学号", "语文", "数学", "英语", "总分");
        printf("\t\t\t-------------------------------------------------------------------\n");
        for (int i = 0; i < number; i++)
        {
            if (stu[i].number == studentnumber)
            {
                printf("\n\t\t\t  %-9s %-9d %-6.1f %-6.1f %-6.1f %-6.1f\n", stu[i].name, stu[i].number, stu[i].chinese, stu[i].math, stu[i].english, stu[i].average);
                printf("\n\n\n");
                printf("\t\t\t--------------------------按任意键返回-----------------------------");
                getch();
                flag1 = 1;
                break;
            }

        }

}

//学生端修改密码

void changeTheSecurityCode(int studentNumber13)
{
    system("cls");
    char tmp1[20];
    int i;

        for (i = 0; i <= nowStudentNumber; i++)
        {
            if (stu[i].number == studentNumber13)
            {
                break;
            }
        }
    printf("\n\n\n");
    printf("\t\t\t--------------------------请输入新密码--------------------------\n");
    printf("\t\t\t");
    scanf("%s", &tmp1);

    strcpy(stu[i].password, tmp1);

    system("cls");

    writeIn();
    printf("\n\n\n");
    printf("\t\t\t--------------------------修改密码成功--------------------------\n");
    printf("\t\t\t--------------------------按任意键返回--------------------------\n");
    getch();
    return 0;
}

//删除学生信息
void deleteAccount()
{
    isStudent Temporary[MAX];//临时数组用来储存内容
    int studentnumber=5;//要求的学生学号
    int number;//学生人数
    int flag = 0;//判断输入是否存在
    int g = 0;//除被删学生外其他人计数

loop5:  system("cls");
    printf("\n\n\n");
    printf("\t\t\t--------------------------请输入要删除的学生学号:----------------------\n");
    printf("\t\t\t----------------------------按“0”返回主菜单-------------------------\n");
    printf("\n\n\t\t\t");
    scanf("%d", &studentnumber);
            getchar();
    if(0==studentnumber)
        return ;

    number = nowStudentNumber;

    for (int i = 0; i < number; i++)
    {
        if (stu[i].number == studentnumber)
        {
            flag = 1;

            stu[i].number = 0;//将该学生学号归零
        }
    }

    if (flag == 0)
    {
        printf("\n\n");
        printf("\t\t\t-------------------不存在该学生学号，请重新输入----------------------\n");
        Sleep(30);
        goto loop5;
    }
    else
    {

        for (int j = 0; j < number; j++)//j是stu内学生
        {
            if (stu[j].number != 0)
            {
                Temporary[g] = stu[j];//按顺序将内容传输

                g++;//这样就是输入一个加一个计数了，判断失败g不加j加一
            }
        }

        char ch;//写入文件

        FILE* pFile;

        pFile = fopen("STUDENT.txt", "w");//状态，覆写

        if (pFile == NULL)
        {

            printf("\t\t\t写入文件失败。\n");
            printf("\t\t\t文件保存错误。\n");
            printf("\t\t\t输入任意键退出。\n");
            getch();
            return 0;//以不正常状态退出
        }
        else
        {

            //将所有内容写入文件
            for (int i = 0; i < g; i++)
            {
                fprintf(pFile, "%-9s %-9d %-6.1lf %-6.1lf %-6.1lf %-6.1lf %-22s\n", Temporary[i].name, Temporary[i].number, Temporary[i].chinese, Temporary[i].math, Temporary[i].english, Temporary[i].average, Temporary[i].password);
            }

            fclose(pFile);
            //getchar();
            readOut();
            printf("\t\t\t--------------------------删除成功！-------------------------------------");
            getch();

        }

    }
}
```
