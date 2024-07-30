**Database**

Database credentials: 
                              
               username :  sa

               Password: P@ssw0rd

run the following command to startup the database engine:
                    
        kubectl apply - f mssql-statfulset.yaml
        
after starting the Pod you will wait till the container is created and running you can check by using :

    kubectl get pod

if you run the 2 commands immediately after each other you will get:
![image](https://github.com/user-attachments/assets/36f478b3-c43b-40a7-8928-e88a9ca757ad)



you can wait for a while and try again till you get:
![image](https://github.com/user-attachments/assets/6871bb3a-838d-427f-b71b-82a8442ebe02)


this means that the container is running and the database engine started.

Connect to Database
the next  step is to create the database for jira:

  for this example we will be using "Microsoft SQL Server Management studio":
  
  start the application and click on connect then configure as following:

         Server name: node IP,31433

![image](https://github.com/user-attachments/assets/38e83e11-0583-4f96-bde4-9b86fe7e94ab)

you can get the node IP using this command:

node IP:

    kubectl get node -o wide
![image](https://github.com/user-attachments/assets/1c0f6e69-0dba-4943-ae79-739f8654d71d)

after connecting the database click on database folder and select New Database:
![image](https://github.com/user-attachments/assets/3feb3869-ccc8-4616-9ab8-12e89af1ae80)

and create new database with the name Jira and the owner is SA

Now the database is ready to go 

and can be used in any database configuration.

----------------------------------------------------------------------------------------------------------------------------------------------------------

**Jira service**

before you start update the ip in the jira-svc.yml file with the node ip

    kubectl apply -f jira-svc.yml
    
check that the service is created and running

    kubectl get svc

![image](https://github.com/user-attachments/assets/f00ee2c6-2588-44b3-82f9-d562054d00c1)

----------------------------------------------------------------------------------------------------------------------------------------------------------

**Shared files folder**

    kubectl apply -f nfs-server-deployment.yaml


![image](https://github.com/user-attachments/assets/6b1b393b-87ed-484d-9ef5-9b49e3363b3a)

wait for a while and check that the nfs-server  is running

    kubectl get pod

![image](https://github.com/user-attachments/assets/ce164b1f-f76d-4ade-993a-7a1b729c9f9c)

Now we can use the nfs to create the volume that will be mounted in the Jira containers as the shared-folder.

**NFS-Server IP**

before we start we need to know the nfs-server ip:

    kubectl get svc

![image](https://github.com/user-attachments/assets/e4b2b536-fc55-4b5f-92a1-537b58a30cfc)


next we will add this ip to the PV file.

----------------------------------------------------------------------------------------------------------------------------------------------------------

**PV (Persistent volume)**

update nfs-pv.yml with nfs-server ip

then create the PV 

    kubectl apply -f nfs-pv.yaml

check that the PV created:

    kubectl get pv

![image](https://github.com/user-attachments/assets/faac00ea-f16d-4ba4-acc2-cc8ab1cc5959)

----------------------------------------------------------------------------------------------------------------------------------------------------------

**PVC (persistent Volume Claim)**

next we will create the PVC (persistent Volume Claim) and Map it to the PV we just created:

    kubectl apply -f nfs-pvc.yaml

check that the PVC created:

    kubectl get pvc

![image](https://github.com/user-attachments/assets/0e86c785-081d-4ba5-9d62-e9a4387d0afe)

----------------------------------------------------------------------------------------------------------------------------------------------------------

**Starting Jira**

after creating every thing you need to make sure that Database is running and having database named Jira with the credentials you added and the NFS-Server is running along with PV and PVC, Make sure that the Jira Port is Exposed correct

*Update the Database configuration in the file with the database ip*
    
    kubectl get svc

![image](https://github.com/user-attachments/assets/ed59fbbe-46ce-4e15-be3c-097d084779b6)


Now you can start jira:
    
    kubectl apply -f jira-values.yaml

    kubectl get pod

you can now wait till the pod is running and access jira from you web browser using the node ip.

----------------------------------------------------------------------------------------------------------------------------------------------------------

**Cluster**

After starting jira and configuring the applictation now you can scale the statefulset and have more than one instance and start you cluster.

you can do it fromthe dash board or you can use:
    
    kubectl scale statefulset jira --replicas=2
