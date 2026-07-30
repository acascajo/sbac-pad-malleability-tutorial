---
layout: default
title: "Part 3 — Demo Execution"
nav_order: 3
---

# Part 3: Demo Execution

---

## 3.1 From Pure MPI to FlexMPI

### The Original MPI Application

```C
#include <stdio.h>
#include <mpi.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#include <math.h>
#include <unistd.h>

int main (int argc, char *argv[])
{
    int it=0;
    int itmax=50;

    // init MPI and get world_rank and world_size
    int world_rank, world_size;
    MPI_Init(&argc, &argv);
    MPI_Comm_rank(MPI_COMM_WORLD, &world_rank);
    MPI_Comm_size(MPI_COMM_WORLD, &world_size);

    // get processor name
    int len;
    char mpi_name[100];
    MPI_Get_processor_name(mpi_name, &len);
    
    printf ("Rank(%d/%d): Start\n", world_rank, world_size);

    // get all processors names
    char local_name[128];
    char global_name[128];
    sprintf(local_name, "%s,", mpi_name);
    
    // shared processors names
    bzero(global_name,128);
    MPI_Allgather(local_name, strlen(local_name), MPI_CHAR, global_name, strlen(local_name), MPI_CHAR, MPI_COMM_WORLD);
                            
    // start loop
    for (; it < itmax; it ++) {
        
        printf("Rank (%d/%d): Iteration= %d, Hello world from: \n\t\t%s\n", world_rank, world_size, it, global_name);

        // simulate compute
        sleep(1);
    }
    printf("Rank (%d/%d): End of loop \n", world_rank, world_size);

    MPI_Finalize();
    
    printf("Rank (%d/%d): End of process %d\n", world_rank, world_size, world_rank);
    if (world_rank == 0) {
        printf("Rank (%d/%d): End of Application\n", world_rank, world_size);
    }

    return 0;
}
```


### Instrumenting with FlexMPI

```C
#include <stdio.h>
#include <mpi.h>
#include <stdlib.h>
#include <string.h>
#include <time.h>
#include <math.h>
#include <unistd.h>
#include <empi.h>

int main (int argc, char *argv[])
{
    int it=0;
    int itmax=50;

    // init MPI and get world_rank and world_size
    int world_rank, world_size;
    MPI_Init(&argc, &argv);
    MPI_Comm_rank(ADM_COMM_WORLD, &world_rank);
    MPI_Comm_size(ADM_COMM_WORLD, &world_size);

    // get processor name
    int len;
    char mpi_name[100];
    MPI_Get_processor_name(mpi_name, &len);

    printf ("Rank(%d/%d): Start\n", world_rank, world_size);

    // get all processors names
    char local_name[128];
    char global_name[128];
    sprintf(local_name, "%s,", mpi_name);
    
    // shared processors names
    bzero(global_name,128);
    MPI_Allgather(local_name, strlen(local_name), MPI_CHAR, global_name, strlen(local_name), MPI_CHAR, ADM_COMM_WORLD);
                            
    // get process type
    int proctype;
    ADM_GetSysAttributesInt ("ADM_GLOBAL_PROCESS_TYPE", &proctype);

    if (proctype == ADM_NATIVE) {
      // if process is native
      printf ("Rank(%d/%d): Process native\n", world_rank, world_size);
    } else {
      // if process is spawned
      printf ("Rank(%d/%d): Process spawned\n", world_rank, world_size);
    }
    
    /* set max number of iterations */
    ADM_RegisterSysAttributesInt ("ADM_GLOBAL_MAX_ITERATION", &itmax);
    /* get actual iteration for new added processes*/
    ADM_GetSysAttributesInt ("ADM_GLOBAL_ITERATION", &it);
    /* starting monitoring service */
    ADM_MonitoringService (ADM_SERVICE_START);

    // init last world zize
    int last_world_size = world_size;
    
    while (it < itmax) {
    
        //Select hints on specific iterations (or periodically)
        int procs_hint = 0;
        int excl_nodes_hint = 0;
        if ( (it == 2*(itmax/10))){
            procs_hint = 3;
            excl_nodes_hint = 1;
            ADM_RegisterSysAttributesInt ("ADM_GLOBAL_HINT_NUM_PROCESS", &procs_hint);
            ADM_RegisterSysAttributesInt ("ADM_GLOBAL_HINT_EXCL_NODES", &excl_nodes_hint);
        } else if ( (it == 6*(itmax/10))){
            procs_hint = -3;
            excl_nodes_hint = 1;
            ADM_RegisterSysAttributesInt ("ADM_GLOBAL_HINT_NUM_PROCESS", &procs_hint);
            ADM_RegisterSysAttributesInt ("ADM_GLOBAL_HINT_EXCL_NODES", &excl_nodes_hint);
        }
        
        // update message if new spawned processes
        if (last_world_size != world_size) {
            // shared processors names
            bzero(global_name,128);
            MPI_Allgather(local_name, strlen(local_name), MPI_CHAR, global_name, strlen(local_name), MPI_CHAR, ADM_COMM_WORLD);
            last_world_size = world_size;
        }
        
        /* start malelability region */
        ADM_MalleableRegion (ADM_SERVICE_START);
        
        printf("Rank (%d/%d): Iteration= %d, Hello world from: \n\t\t%s\n", world_rank, world_size, it, global_name);

        // simulate compute
        sleep(1);
    
        // increrease iteration
        it++;
        
        // update the iteration value
        ADM_RegisterSysAttributesInt ("ADM_GLOBAL_ITERATION", &it);
        
        // ending malleable region
        int status;
        status = ADM_MalleableRegion (ADM_SERVICE_STOP);
        
        if (status == ADM_ACTIVE) {
          // update world_rank and size
          MPI_Comm_rank(ADM_COMM_WORLD, &world_rank);
          MPI_Comm_size(ADM_COMM_WORLD, &world_size);
        } else {
          // end the process
          break;
        }
    }

    /* ending monitoring service */
    ADM_MonitoringService (ADM_SERVICE_STOP);
    
    printf("Rank (%d/%d): End of loop \n", world_rank, world_size);

    MPI_Finalize();

    printf("Rank (%d/%d): End of process %d\n", world_rank, world_size, world_rank);
    if (world_rank == 0) {
        printf("Rank (%d/%d): End of Application\n", world_rank, world_size);
    }

    return 0;
}
```

### Step 4: Before-and-After Comparison

With FlexMPI, the application can scale up three processes in iteration 10, and can return to its normal size in iteration 30.

How to define this behaviour can be seen int the following piece of code: 

```C
if ( (it == 2*(itmax/10))){
  procs_hint = 3;
  excl_nodes_hint = 1;
  ADM_RegisterSysAttributesInt ("ADM_GLOBAL_HINT_NUM_PROCESS", &procs_hint);
  ADM_RegisterSysAttributesInt ("ADM_GLOBAL_HINT_EXCL_NODES", &excl_nodes_hint);
} else if ( (it == 6*(itmax/10))){
  procs_hint = -3;
  excl_nodes_hint = 1;
  ADM_RegisterSysAttributesInt ("ADM_GLOBAL_HINT_NUM_PROCESS", &procs_hint);
  ADM_RegisterSysAttributesInt ("ADM_GLOBAL_HINT_EXCL_NODES", &excl_nodes_hint);
}
```

Each *if* sentence identifies an iteration number. In those iterations, one malleable operation is requested. In the first conditional, variable *procs_hint* is 3, which means "expand 3 processes". On the other hand, int the second conditional, variable *procs_hint* is -3, which means "reduce 3 processes".  


---

## 3.2 Live Demo 

Please, follow the intructor during the hands on. 

---

## 3.3 (Optional) Malleable Execution Video with a real application and a real scenario


---

<nav style="display: flex; justify-content: space-between; margin-top: 3rem;">
  <a href="02-preparing-the-setup">← Previous: Part 2</a>
  <a href="../index">Back to Home →</a>
</nav>
