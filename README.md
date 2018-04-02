/*
Ques. 
7
. 
Design a scheduling program to implements a Queue with two levels:
Level 1 : Fixed priority preemptive Sche
duling
Level 2 : Round Robin Scheduling
For a Fixed priority preemptive Scheduling (Queue 1), the Priority 0 is highest priority. If one 
process P1 is scheduled and running , another process P2 with higher priority comes. The New 
process (high priority) pr
ocess P2 preempts currently running process P1 and process P1 will go 
to second level queue. Time for which process will strictly execute must be considered in the 
multiples of 2..
All the processes in second level queue will complete their execution accor
ding to round robin 
scheduling.
Consider: 1. Queue 2 will be processed after Queue 1 becomes empty.
2. Priority of Queue 2 has lower priority than in Queue 1.
*/
#include<stdio.h>
#include<iostream>
#include<queue>

using namespace std;

#define MAX 10000
#define QUANTA 4

int flag[MAX],at[MAX],bt[MAX],pt[MAX],rt[MAX],ft[MAX],fe[MAX],fe_flag[MAX],pid[MAX],tms,qt[MAX];

//at stands for arrival time
//bt stands for burst time
//rt rt stands for Response Time
//pt stands for priority
//pid  staands for process id

queue<int> q;  //RoundRobin queue

void RoundRobin()
{
      if(!q.empty())
      {
      	if(rt[q.front()]>0 && qt[q.front()]<4)
      	{
          		qt[q.front()]++;
          		rt[q.front()]--;
          		if(rt[q.front()]==0)
          		{
            	ft[q.front()]=tms+1;
            	q.pop();
          		}
          		if(rt[q.front()]!=0 && qt[q.front()]==4)
          		{
				qt[q.front()]=0;
				q.push(q.front());
				q.pop();
          		}
        	}
      }
}

int main()
{
    int i=0,n=0,smallest=0,last_smallest=-1,min,sum=0,large=0;
    scanf("%d",&n);
    for(i=0;i<n;i++)
    {
    		scanf("%d %d %d %d",&pid[i],&at[i],&bt[i],&pt[i]);
    		if(at[i]>large)
    		  	large=at[i];
    		  sum+=bt[i];
    		  rt[i]=bt[i];
    }
    min=MAX;
    for(tms=0;!q.empty() || tms<=sum+large ;tms++)
    {
      min=MAX;
      smallest=-1;
      for(i=0;i<n;i++)
      {
      	if(at[i]<=tms && pt[i]<min && rt[i]>0 && !flag[i])
      	{
      		min=pt[i];
          		smallest=i;
        	}
      }
      if(smallest==-1 && !q.empty())
      {
      	if(last_smallest !=-1 && rt[last_smallest]==0)
      	{
      		ft[last_smallest]=tms;
          		flag[last_smallest]=1;
        	}
        	last_smallest=-1;
        	RoundRobin();
        	continue;
      }
      else if(smallest!=-1 && !q.empty() && last_smallest==-1)
      {
      	if(qt[q.front()]<=4 && qt[q.front()]>0)
      	{
      		q.push(q.front());
      		q.pop();
        	}
      }
      if(smallest!=-1 && !fe_flag[smallest])
      {
      	fe[smallest]=tms-at[smallest];
      	fe_flag[smallest]=1;
      }
      if( smallest!=last_smallest && last_smallest!=-1 && !flag[last_smallest])
      {
      	q.push(last_smallest);
      	flag[last_smallest]=1;
      }
      if(smallest !=-1)
      	rt[smallest]--;
      
      if((smallest !=-1) && ((rt[smallest]==0) ||(bt[smallest]-rt[smallest])==QUANTA))
      {
      	if((bt[smallest]-rt[smallest])==QUANTA && rt[smallest]!=0)
      	{
      		flag[smallest]=1;
      		q.push(smallest);
        	}
       	else if(smallest!=-1)
       	{
          		ft[smallest]=tms+1;
          		flag[smallest]=1;
        	}
      }
      last_smallest=smallest;
    }
   for(int i=0;i<n;i++)
      cout<<pid[i]<<" "<<fe[i]<<" "<<ft[i]<<" "<<ft[i]-bt[i]-at[i]<<endl;
return 0;
}
