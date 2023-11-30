#include<sys/types.h>
#include<sys/sem.h>
#include<sys/shm.h>
#include<unistd.h>
#include<stdio.h>
#include<errno.h>
#include<time.h>
#include<stdlib.h>
union semun{
	int val;
	struct semid_ds *buf;
	unsigned short *array;
};
#define MAXSUM 1
int SA;
int SO;
int mutexid;


int main(){
	struct sembuf P,V;
	union semun arg;
	
	
//	int arrayid;
//	int getid;
//	int *array;
//	int *get;
	int plate;
	
//	arrayid=shmget (IPC_PRIVATE,sizeof(int)*MAXSHM, IPC_CREAT | 0666);
//	getid=shmget(IPC_PRIVATE,sizeof(int),IPC_CREAT|0666);
	
//	array= (int*)shmat(arrayid,0,0);
	plate=0;
//	get=(int*)shmat(getid,0,0);
//	*get=0;

	SA=semget(IPC_PRIVATE,1,IPC_CREAT|0666);
	SO=semget(IPC_PRIVATE,1,IPC_CREAT|0666);
	mutexid=semget(IPC_PRIVATE,1,IPC_CREAT|0666);
	
	arg.val =0;
	if(semctl(SA,0,SETVAL,arg)==-1) perror("semctl setval error");
	arg.val = 0;
	if(semctl(SO,0,SETVAL,arg)==-1) perror("semctl setval error");
	arg.val = l;
	if(semctl(mutexid,0,SETVAL,arg)==-1) perror("setctl setval error");

	P.sem_num=0;
	P.sem_op=-1;
	P.sem_flg=SEM_UNDO;
	V.sem_num=0;
	V.sem_op=1;
	V.sem_flg=SEM_UNDO;
	
	 
 	if(fork()==0){
 		int i=0;
 		while(i<10){
 			semop(mutexid,&P,1);
	 		plate=1;
	 		printf("Mom puts an orange");
	 		semop(SO,&V,1);
	 		i++;
		}
		sleep(3);
		print("Mom is over");
		exit(0);
	}else{
		if(fork()==0){
			int i=0;
 			while(i<10){
				semop(mutexid,&P,1);
	 			plate=2;
	 			printf("Dad puts an apple");
	 			semop(SA,&V,1);
	 			i++;
	 		}
 			sleep(3);
 			print("Dad is over");
 			exit(0);
		}else{
			if(fork()==0){
				while(1){
					if(plate==0) break;
					semop(SO,&P,1);
					printf("Son eats an orange");
					plate=0;
				}
				print("Son is over");
				exit(0);
			}else{
				if(fork()==0){
					while(1){
						if(plate==0) break;
						semop(SA,&P,1);
						printf("Daughter eats an apple");
						plate=0;
					}
					print("Daughter is over");
					exit(0);
				}
			}
		}
		wait(0);
		wait(0);
		wait(0);
		wait(0);
		semct1(SA,IPC_RMID,0);
		semct1(SO,IPC_RMID,0);
		semct1(mutexid,IPC_RMID,0);
	}
}