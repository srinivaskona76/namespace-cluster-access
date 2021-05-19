# namespace-cluster-access
User pod-reader having admin access with two namespaces(ns1 and ns2) and read access to cluster level(all namespaces) objects.

1. For this process I first created eks cluster with IAM user as cloud-user(admin access) by eksctl command as followed.

eksctl create cluster --name test-cluster --version 1.18 --region us-east-1 --nodegroup-name linux-nodes \
  --node-type t2.micro --nodes 2 --zones us-east-1a,us-east-1b,us-east-1c

By the above command it created EKS cluster with 2 Nodes.

2. I created pod-reader IAM user with programatic access with no-access to any services in AWS and configured with aws configure in seperate terminal.
   - In order to configure user(pod-reader) with cluster we need to modify configmap  aws-auth in kube-system namespace mapUsers field as follows.
   FROM 
       mapUsers: |
       []
   TO    
       mapUsers: |
         - userarn: arn:aws:iam::809411733541:user/pod-reader
           username: pod-reader
           groups:
             - pod-reader 

3. Ater configured pod-reader with cluster, I have created following objects and applied.
   - ns1.yaml
   - ns2.yaml
   - ns3.yaml
   - ns1-role.yaml
   - ns2-role.yaml
   - ns1-role-binding.yaml
   - ns2-role-binding.yaml
   - read-cluster-role.yaml
   - reader-cluster-role-binding.yaml
   - ns1nginx.yaml
   - ns2nginx.yaml
   - ns3nginx.yaml

4. By applying all the objects following objects will be created.
   - ns1, ns2 and ns3 namespaces will be created.
   - Roles(ns1-role and ns2-role) with admin access to namsespaces ns1 and ns2 will be created.
   - RoleBindings(ns1-role-binding and ns2-role-binding) with  ns1-role and ns2-role with user pod-reader will be created.
   - Cluster-Role(read-cluster-role) with get,watch,list access to cluster wide Resources will be created.
   - Cluster-Role-Binding(cluster-role-binding) with user pod-reader and Role read-cluster-role will be attached. 
   - Pods with Nginx image will be deployed in all three namespaces(ns1,ns2 and ns3) will be created.
   - By this all set-up User pod-reader can have admin access to ns1 and ns2 namespaces.
   - pod-reader can have read access to all other namespaces other than ns1 and ns2. 





 


