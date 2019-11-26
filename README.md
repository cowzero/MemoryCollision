# memorycollision
1. 개요
Stack과 Heap이 공유하는 메모리 segment를 확인한다.
-Stack과 Heap을 증가시키면서 충돌하는 지점을 찾는다.
-Stack과 Heap의 각각의 크기와 총 메모리 Segment의 크기를 구한다.
-Stack과 Heap의 크기를 달리하여 다른 지점에서 충돌시켜 처음 경우와 비교한다.


2. 프로그램 구조 설명
2.1 함수에 관한 설명

- void  RecursiveRoutine(int RecursiveDepth)
: 재귀적으로 호출되는 함수. 딱히 하는 일은없지만 변수를 추가하면서 stack의 공간을 사용한다.
Memory를 다 사용하면 무너질 것으로 예상된다.

-char *commas(unsigned long amount)
: unsigned int를 콤마가 있는 문자열로 바꾸는 함수

-int prepend(char *, unsigned, char *)
: 문자열을 덧붙이는 함수

-int preprintf(char *, unsigned, char *, ...)
:문자열을 덧붙이는 함수


2.2 다이어그램
(함수가 딱히 없으므로 코드를 대략적으로 도시겠습니다.)
①MallocTooMuch.c - Heap영역


②recursivecall.c – Stack영역



③hw.4


3. 실행 결과
①Heap의 총 메모리 크기 구하기
- gcc –g MallocTooMuch.c –o MallocTooMuch
  ./MallocTooMuch 12345678 Nothing을 쳤을 때  

메모리가 다 찼다면 “The program is ending~”이라는 문구가 뜨고 종료해야 하는데 그렇지 않고 kille되었다. 이는 리소스 부족이나 버그로 인해서 강제로 종료되었다고 볼 수 있는데, 이 때문에 정확한 Heap의 메모리 크기를 구할 수 없었다. 그래서 대략 905600 Mbytes 이상으로 추정했다.

- ./MallocTooMuch 12345678 Read를 쳤을 때  

이 또한 마찬가지였다.

- ./MallocTooMuch 12345678 Write를 쳤을 때

Write를 했을 때 컴퓨터가 더 힘이 들었는지 메모리할당을 조금 밖에 못하고 죽어버렸다.
세 경우를 종합했을 때 Heap의 메모리는 905600 Mbytes 이상으로 추정했다.

②Stack의 총 메모리 크기 구하기
- ulimit –s unlimited
  gcc recursivecall.c –o recursivecall
  ./recursivecall을 쳤을 때
stack에 용량제한이 걸려있어 제한을 해제한 후에 실행해 보았다

Stack의 메모리가 1131080012bytes까지 됐을 때 killed되었다. 때문에 정확한 메모리는 알 수 없었고 Stack의 메모리가 1131080012bytes 이상이라고 추정할 수 있다.

-두번째 시도


마찬가지로 killed되었다. 이번에는 1297749580bytes 까지 메모리를 할당하고 죽었다. 두 경우를 고려하면 stack은 적어도 1297749580bytes이상임을 알 수 있다.








③Stack과 Heap의 충돌하는 지점 찾기
- gcc –g hw4.c –o hw4
  ./hw4 500000 Nothing을 쳤을 때

Heap이 900000Mbytes정도를 넘으면 killed되므로 정확한 충돌을 확인할 수 없기 때문에 Heap에 500000Mbytes를 할당하고, Stack이 overflow 되는 지점을 확인해 보았다..
Heap에 500000Mbytes를 할당하고 Stack을 한다

충돌하는 지점까지 가지 못하고 killed 되었다. heap에 500000Mbytes를 할당했을 때 stack이 적어도 1317676232bytes이상이 되어야 충돌한다고 볼 수 있다.

- ./hw4 100000 Nothing을 쳤을 때
Heap에 100000Mbytes를 할당하고, Stack overflow 되는 지점을 확인해 보았다.

Heap에 100000Mbytes를 할당하고 Stack을 한다.

충돌하는 지점까지 가지 못하고 killed 되었다. heap에 100000Mbytes를 할당했을 때 stack이 적어도 2001902792bytes이상이 되어야 충돌한다고 볼 수 있다.

컴퓨터가 사양이 안 좋아서 메모리가 다 찰 때까지 메모리를 할당하지 않고 종료된 것으로 보였다. 그래서 정확한 충돌 지점을 찾을 수는 없었지만, heap에 500000Mbytes을 할당했을 때보다 100000Mbytes를 할당했을 때가 stack의 메모리가 더 컸다. 이는 Stack과 Heap이 메모리 공간을 공유하므로 Heap의 크기가 줄어든다면 Stack의 크기는 당연히 커진다고 볼 수 있다.







④다른 컴퓨터에서 윈도우로 실행한 경우.
-Heap의 총 메모리 용량

여기서는 Heap의 메모리가 1899Mbytes임을 알 수 있다. 이 컴퓨터는 Heap의 용량이 제한이 되어있어서 할당할 수 있는 메모리가 적었다.

-Stack의 총 메모리 용량

Stack의 메모리 용량이 973872임을 알 수 있다. 이 컴퓨터에서 Stack의 메모리 또한 용량이 제한되어 있어서 용량이 적었다.




-Heap에 1000Mbytes 할당 후 Stack에 메모리 할당


이 경우 Stack 메모리 용량이 별 차이가 없었는데 이는 Heap과 Stack영역 둘다 용량에 제한이 있어서 충돌이 발생하지 않았다고 볼 수 있다.


4. 고찰

메이크 파일을 할 때 gcc –g MallocTooMuch.c –o MallocTooMuch ./MallocTooMuch 를 넣으니까 게속 에러가 났다. 왜 그런지 영문을 모르다가 ./MallocTooMuch 하고 인자 2개를 더 넣어야 하는데 그렇지 않아서 그런 것이었다. 그래서 그 후에는 메이크를 하지 않고 그냥 명령어 창에다가 입력했다. 
 가상머신에서 프로그램을 돌렸는데, 제대로 작동하지 않아 당황스러웠다. Stack과 Heap의 total메모리를 알아야 하는데 Total memory까지 할당하기도 전에 컴퓨터가 작동하지 않아서 정확한 메모리를 알 수 없었다. 처음에는 컴퓨터가 에러난 상황인 줄도 몰라서 왜 실행할 때마다 메모리가 다르지?라는 의문이 들었는데 가상머신이 메모리가 다 찰 때까지 메모리를 할당해주지 못하고 죽어버렸기 때문이었다.... 총 용량을 알아야 충돌하는 지점을 찾을 수 있을 것 같은데 그러지 못해서 갑갑했다. stack과 heap을 각각 증가시키면서 충돌하는 지점을 찾기 위해 동시에 두 터미널에서 돌려보았는데 각각 돌렸을 때와 별 다를 바가 없었다. 그래서 한 파일에 Heap을 한후 stack을 하도록 해 보았다. 이렇게 하니 각각 증가시키면서 충돌하는 지점은 찾을 수는 없었다. 그래도 heap의 크기를 조절하면서 충돌하는 지점은 볼 수 있었다. 만약 컴퓨터가 에러가 나지 않는다면 두 터미널에서 동시에 돌린다면 가능할 것 같다는 생각이 들었다. 아니면 두 개의 쓰레드로 돌려봐야할 것 같다. 다른 컴퓨터로 프로그램을 돌려보았는데 그 컴퓨터에는 리눅스가 없어서 윈도우로 실행해 보았다. 그러나 그 컴퓨터는 stack과 heap에 용량 제한이 걸려 있어서 총 용량을 알 수는 있었지만 충돌하는 지점을 찾기는 어려웠다. heap의 크기를 조절하면서 충돌 지점을 찾으면 그때마다 stack의 메모리 용량 또한 달라져야 한다고 생각하는데 이 또한 컴퓨터가 메모리를 다 할당하기도 전에 끝나서이기 때문인지 stack의 메모리 용량에 변화가 없었다. 내가 생각했던 것을 실제로 결과로 확인해 볼 수 없어서 아쉬움이 많이 남았다. 다른 컴퓨터의 가상머신으로 돌려보았지만 그것도 도중에 kill당해서 결과는 별 다를 바가 없었다. 가상머신이 아닌 진짜 우분투에서 실행했다면 결과가 다르지 않았을까 싶어서 또 다른 컴퓨터에 가상머신이 아닌 진짜 우분투를 깔아서 실행해 보았는데 실행 도중 컴퓨터가 렉이 걸려서 결과를 캡처할 순 없었다. 
 컴퓨터의 용량을 다루는 것은 이번이 처음인데 생소하고 어려웠다. 그러나 stack은 함수 콜 할 때, 정적 할당, heap은 동적할당 시 메모리 영역이라는 것을 이론으로만 알다가 실제로 다뤄보니까 더 기억에 확실히 남고 좋은 경험이었다. 비록 원하는 결과를 얻어내진 못하였지만 메모리와 친숙해진 것 같아 좋았다.

5. 프로그램 소스 파일
<MallocTooMuch.c> : Heap영역에 메모리를 할당한다
/*
  TouchLotsOfMemory.c
  This program allocates memory from the heap and 
  1.  Touches it by reading from it.
  2.  Modifies it by writing to it.
  3.  Neither - the memory is never accessed.

  Arg 1 - The memory size (in Megabytes) to be allocated.
  Arg 2 - Specify <Read, Write, Neither> for touching the memory.
          Compiler Instructions:
          Windows:  gcc -g TouchLotsOfMemory.c -o TouchLotsOfMemory

  Running Hints:  You can use top to find the total memory on LINUX system
                  vmstat -5 gives running account of memory usage.
*/

#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <string.h>

#define   ONE_MEG   1048576
//  Prototypes  &  Globals
long *MemoryPtr;

//  The main code is here
int main(int argc, char *argv[]){

    long NumberOfMegaBytes, NumberOfAllocations = 0;
    long Temp;
    long *TempPointer;
    char MemoryFunction[32];
    int  Function = 0;

    if (argc < 3 ) {//인자가 3개 미만이면 종료한다
        printf( "Usage: %s <MemorySizeInMegabyte> <Read|Write|Nothing>\n", argv[0]);
        exit(1);
    }

    //  Get the arguments and validate them
    NumberOfMegaBytes = atoi( argv[1] ); //메모리 크기를 정수형으로 변환한 후 저장한다
    strcpy( MemoryFunction, argv[2] ); //옵션을 MemoryFunction에 저장한다

    if ( strncmp ( MemoryFunction, "Nothing", 7 ) == 0 ) Function = 1;
    if ( strncmp ( MemoryFunction, "Read", 4 ) == 0    ) Function = 2;
    if ( strncmp ( MemoryFunction, "Write", 5 ) == 0   ) Function = 3;
 
    if ( Function == 0 )  {
        printf( "Unable to recognize the Read|Write|Nothing portion of the command\n");
	return;
    }//Function이 0이면 Nothing, Read, write중 아무것도 선택 안한 것이므로 종료한다

    //  Now loop through each of the megabytes and perhaps touch each of the 1024 pages
    //  in that megabyte.
    while ( NumberOfAllocations < NumberOfMegaBytes ) {//원하는 메모리까지 할당할 때까지 반복
        MemoryPtr = ( long * ) malloc( ONE_MEG ); //1Mbytes만큼 할당한다

        if ( MemoryPtr == 0 ) {//MemoryPtr이 0이면 메모리 할당을 하지 못한 것이므로 종료한다
            printf( "The program is ending because we could allocate no more memory.\n");
	    printf( "Total megabytes allocated = %d\n", NumberOfAllocations );
            exit(0);
        }
	NumberOfAllocations++;//MemoryPtr이 0이 아니면 메모리 할당을 성공하였으므로 증가한다 
	if ( ( NumberOfAllocations % 100 ) == 0 )  // Print out status every so often
		printf( "We have allocated %d Megabytes\n", NumberOfAllocations );
	//100마다 출력한다

	TempPointer = MemoryPtr;

	if ( Function == 2 ){ // 메모리를 할당한 후에 읽는 과정
	    while ((unsigned long)TempPointer < (unsigned long)((long)MemoryPtr + ONE_MEG)) {
	    //Memory를 1Mbytes만큼 할당했으므로 1Mbytes의 첫 주소부터 1M번째 주소까지 읽는다 
                Temp = TempPointer[0]; //Temp에 TempPonter안의 내용을 읽어온다
                TempPointer = (long *)((unsigned long)TempPointer+4096);//그 다음 주소를 가리킴
            }  // End of while
        }  // End of if Function is Read

	if ( Function == 3 ){ //메모리를 할당한 후에 쓰는 과정
	    while ((unsigned long)TempPointer < (unsigned long)((long)MemoryPtr + ONE_MEG)) {
	   //Memory를 1Mbytes만큼 할당했으므로 1Mbytes의 첫 주소부터 1M번째 주소까지 쓴디
                TempPointer[0] = Temp; //TempPonter에 Temp의 내용을 쓴다
                TempPointer = (long *)((unsigned long)TempPointer+4096);//그 다음 주소를 가리킴
            }  // End of while
        }  // End of if Function is Write
    } // End of while memory still can be allocated
}  // End of main


<recursivecall.c> : Stack영역에 재귀적으로 함수 콜을 해서 overflow가 일어나도록 유도한다.
/*
  Recusive.c
  This program is designed to show memory layout by repeatedly calling
  a subroutine and allocating stack data on each call.
  This uses up lots of stack until eventually no more stack space can
  be allocated and the program crashes.
  This stack data is being touched simply because return addresses
  are being pushed.

  Compile with  gcc recursive.c -o rc.
  This SHOULD work with 32 or 48 bit logical addresses
*/

#include  <stdio.h>
#include  <string.h>

#define   ONE_MEG              1048576

//  Prototypes
void     RecursiveRoutine( int );
char     *commas(unsigned long amount);

//  This is a global so the subroutine can see it.
unsigned long    FirstStackLocation;

int main( int argc, char  *argv[] ){

    int    TopOfStack;
    int    Counter = 0;

    FirstStackLocation = (unsigned long)(&TopOfStack); //Stack의 첫 위치
    printf("First location on stack: %s\n", commas( (unsigned long)FirstStackLocation  ) );
  
    RecursiveRoutine( 0 );
}

//  This routine is called recursively.  It does no real work but adds
//  a variable to the stack which uses up space on the stack.
//  It's expected that the program will crash when memory is exhausted.
#define    STACK_ALLOC    ONE_MEG/20

void  RecursiveRoutine( int RecursiveDepth )
{
    char    Temp[ STACK_ALLOC ];
    char    StringTop[32];
    char    StringBottom[32];

    strcpy( StringTop,  commas( (unsigned long)(FirstStackLocation) ) );
    strcpy( StringBottom, commas( (unsigned long)&(Temp[STACK_ALLOC]) ) )
;
    printf("Iteration = %3d:  Stack Top/Bottom/Bytes: %s  %s  %d\n", 
             RecursiveDepth, StringTop, StringBottom,
             FirstStackLocation - (unsigned long)&(Temp[STACK_ALLOC])  );
//첫 주소에서 할당된 만큼을 빼기 때문에 stack의 총 메모리임을 알 수 있다

    RecursiveDepth++;
    RecursiveRoutine( RecursiveDepth ); //재귀함수
}

//  The following routines are for formatting only and aren't needed for
//  an understanding of the memory manipulations done above.

#define BASE 16
#define GROUP 4
#define MAXTEXT 25 

/*
 This routine converts an unsigned integer into a string with commas.
 You may need to adjust the base and the number of digits between
 commas as given by BASE and GROUP.
 Need space to hold the digits of an unsigned int,
 intervening commas and a null byte. It depends on
 BASE and GROUP above (but logarithmically, not
 as a constant. so we must define it manually here)
*/

int prepend(char *, unsigned, char *);
int preprintf(char *, unsigned, char *, ...);

char *commas(unsigned long amount)
    {
    short i;
    short offset = MAXTEXT-1;   /* where the string "starts"  */
    short place;                /* the power of BASE for      */
                                /* current digit              */
    static char text[MAXTEXT];

    for ( i = 0; i < MAXTEXT; i++ )
        text[i] = '\0';
					     
   /* Push digits right-to-left with commas */
   for (place = 0; amount > 0; ++place)
      {
      if (place % GROUP == 0 && place > 0)
         offset = prepend(text,offset,",");
      offset = preprintf(text,offset,"%X",amount % BASE);
      amount /= BASE;
    }
    return (offset >= 0) ? text + offset : NULL;
}
    /* preprint.c: Functions to prepend strings */
    
#include <stdio.h>
#include <string.h>
#include <stdarg.h>
#include <stdlib.h>
    
int prepend(char *buf, unsigned offset, char *new_str)
    {
    int new_len = strlen(new_str);
    int new_start = offset - new_len;
    /* Push a string onto the front of another */
    if (new_start >= 0)
        memcpy(buf+new_start,new_str,new_len);
    
    /* Return new start position (negative if underflowed) */
    return new_start;
}
    
int preprintf(char *buf, unsigned offset, char *format, ...)
    {
    int pos = offset;
    char *temp = malloc(BUFSIZ);
    
    /* Format, then push */
    if (temp)
        {
        va_list args;
        
        va_start(args,format);
        vsprintf(temp,format,args);
        pos = prepend(buf,offset,temp);
        va_end(args);
        free(temp);
    }
    return pos;
}


<hw4.c> : Heap영역에 메모리를 할당한 후 Stack영역에 메모리를 할당한다.
/*
  Recusive.c
  This program is designed to show memory layout by repeatedly calling
  a subroutine and allocating stack data on each call.
  This uses up lots of stack until eventually no more stack space can
  be allocated and the program crashes.
  This stack data is being touched simply because return addresses
  are being pushed.

  Compile with  gcc recursive.c -o rc.
  This SHOULD work with 32 or 48 bit logical addresses
*/

#include <stdio.h>
#include <stdlib.h>
#include <math.h>
#include <string.h>

#define   ONE_MEG   1048576

//  Prototypes 
void     RecursiveRoutine( int );
char     *commas(unsigned long amount);
//  This is a global so the subroutine can see it.
unsigned long    FirstStackLocation;
long       *MemoryPtr;

//  The main code is here
int main(int argc, char *argv[]){

    int    TopOfStack;
    int    Counter = 0;

    long         NumberOfMegaBytes, NumberOfAllocations = 0;
    long         Temp;
    long         *TempPointer;
    char         MemoryFunction[32];
    int          Function = 0;

    if (argc < 3 ) {//인자가 3개 미만이면 종료한다
        printf( "Usage: %s <MemorySizeInMegabyte> <Read|Write|Nothing>\n", argv[0]);
        exit(1);
    }

    //  Get the arguments and validate them
    NumberOfMegaBytes = atoi( argv[1] ); //메모리 크기를 정수형으로 변환한 후 저장한다
    strcpy( MemoryFunction, argv[2] ); //옵션을 MemoryFunction에 저장한다

    if ( strncmp ( MemoryFunction, "Nothing", 7 ) == 0 ) Function = 1;
    if ( strncmp ( MemoryFunction, "Read", 4 ) == 0    ) Function = 2;
    if ( strncmp ( MemoryFunction, "Write", 5 ) == 0   ) Function = 3;
 
    if ( Function == 0 )  {
        printf( "Unable to recognize the Read|Write|Nothing portion of the command\n");
	return;
    }//Function이 0이면 Nothing, Read, write중 아무것도 선택 안한 것이므로 종료한다

    //  Now loop through each of the megabytes and perhaps touch each of the 1024 pages
    //  in that megabyte.
    while ( NumberOfAllocations < NumberOfMegaBytes ) {//원하는 메모리까지 할당할 때까지 반복
        MemoryPtr = ( long * ) malloc( ONE_MEG ); //1Mbytes만큼 할당한다

        if ( MemoryPtr == 0 ) {//MemoryPtr이 0이면 메모리 할당을 하지 못한 것이므로 종료한다
            printf( "The program is ending because we could allocate no more memory.\n");
	    printf( "Total megabytes allocated = %d\n", NumberOfAllocations );
            exit(0);
        }
	NumberOfAllocations++;//MemoryPtr이 0이 아니면 메모리 할당을 성공하였으므로 증가한다 
	if ( ( NumberOfAllocations % 100 ) == 0 )  // Print out status every so often
		printf( "We have allocated %d Megabytes\n", NumberOfAllocations );
	//100마다 출력한다

	TempPointer = MemoryPtr;

	if ( Function == 2 ){ // 메모리를 할당한 후에 읽는 과정
	    while ((unsigned long)TempPointer < (unsigned long)((long)MemoryPtr + ONE_MEG)) {
	    //Memory를 1Mbytes만큼 할당했으므로 1Mbytes의 첫 주소부터 1M번째 주소까지 읽는다 
                Temp = TempPointer[0]; //Temp에 TempPonter안의 내용을 읽어온다
                TempPointer = (long *)((unsigned long)TempPointer+4096);//그 다음 주소를 가리킴
            }  // End of while
        }  // End of if Function is Read

	if ( Function == 3 ){ //메모리를 할당한 후에 쓰는 과정
	    while ((unsigned long)TempPointer < (unsigned long)((long)MemoryPtr + ONE_MEG)) {
	   //Memory를 1Mbytes만큼 할당했으므로 1Mbytes의 첫 주소부터 1M번째 주소까지 쓴디
                TempPointer[0] = Temp; //TempPonter에 Temp의 내용을 쓴다
                TempPointer = (long *)((unsigned long)TempPointer+4096);//그 다음 주소를 가리킴
            }  // End of while
        }  // End of if Function is Write
    } // End of while memory still can be allocated
    FirstStackLocation = (unsigned long)(&TopOfStack); //Stack의 첫 위치
    printf("First location on stack: %s\n", commas( (unsigned long)FirstStackLocation  ) );
  
    RecursiveRoutine( 0 );
} // End of main

//  This routine is called recursively.  It does no real work but adds
//  a variable to the stack which uses up space on the stack.
//  It's expected that the program will crash when memory is exhausted.
#define    STACK_ALLOC    ONE_MEG/20

void  RecursiveRoutine( int RecursiveDepth )
{
    char    Temp[ STACK_ALLOC ];
    char    StringTop[32];
    char    StringBottom[32];

    strcpy( StringTop,  commas( (unsigned long)(FirstStackLocation) ) );
    strcpy( StringBottom, commas( (unsigned long)&(Temp[STACK_ALLOC]) ) )
;
    printf("Iteration = %3d:  Stack Top/Bottom/Bytes: %s  %s  %d\n", 
             RecursiveDepth, StringTop, StringBottom,
             FirstStackLocation - (unsigned long)&(Temp[STACK_ALLOC])  );
//첫 주소에서 할당된 만큼을 빼기 때문에 stack의 총 메모리임을 알 수 있다

    RecursiveDepth++;
    RecursiveRoutine( RecursiveDepth ); //재귀함수
}

//  The following routines are for formatting only and aren't needed for
//  an understanding of the memory manipulations done above.

#define BASE 16
#define GROUP 4
#define MAXTEXT 25 

/*
 This routine converts an unsigned integer into a string with commas.
 You may need to adjust the base and the number of digits between
 commas as given by BASE and GROUP.
 Need space to hold the digits of an unsigned int,
 intervening commas and a null byte. It depends on
 BASE and GROUP above (but logarithmically, not
 as a constant. so we must define it manually here)
*/

int prepend(char *, unsigned, char *);
int preprintf(char *, unsigned, char *, ...);

char *commas(unsigned long amount)
    {
    short i;
    short offset = MAXTEXT-1;   /* where the string "starts"  */
    short place;                /* the power of BASE for      */
                                /* current digit              */
    static char text[MAXTEXT];

    for ( i = 0; i < MAXTEXT; i++ )
        text[i] = '\0';
					     
   /* Push digits right-to-left with commas */
   for (place = 0; amount > 0; ++place)
      {
      if (place % GROUP == 0 && place > 0)
         offset = prepend(text,offset,",");
      offset = preprintf(text,offset,"%X",amount % BASE);
      amount /= BASE;
    }
    return (offset >= 0) ? text + offset : NULL;
}
    /* preprint.c: Functions to prepend strings */
    
#include <stdio.h>
#include <string.h>
#include <stdarg.h>
#include <stdlib.h>
    
int prepend(char *buf, unsigned offset, char *new_str)
    {
    int new_len = strlen(new_str);
    int new_start = offset - new_len;
    /* Push a string onto the front of another */
    if (new_start >= 0)
        memcpy(buf+new_start,new_str,new_len);
    
    /* Return new start position (negative if underflowed) */
    return new_start;
}
    
int preprintf(char *buf, unsigned offset, char *format, ...)
    {
    int pos = offset;
    char *temp = malloc(BUFSIZ);
    
    /* Format, then push */
    if (temp)
        {
        va_list args;
        
        va_start(args,format);
        vsprintf(temp,format,args);
        pos = prepend(buf,offset,temp);
        va_end(args);
        free(temp);
    }
    return pos;
} 


6. 자료 출처
http://blogfiles.naver.net/20160110_73/tkdldjs35_1452405000525Qzfcf_PNG/2016-01-10_14%3B46%3B34.PNG
