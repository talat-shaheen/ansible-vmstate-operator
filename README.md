# ansible-vmstate-operator
cd gittest/
git clone git@github.com:talat-shaheen/ansible-vmstate-operator.git
ls
cd ansible-vmstate-operator/
operator-sdk init --plugins=ansible  --domain xyzcompany.com
operator-sdk create api --group aws --version v1 --kind AWSEC2  --generate-role       --generate-playbook
operator-sdk create api --group aws --version v1 --kind AWSManager  --generate-role       --generate-playbook
make docker-build IMG=" quay.io/talat_shaheen0/ansible-vmstate-operator:latest";
sudo make docker-push  IMG=" quay.io/talat_shaheen0/ansible-vmstate-operator:latest";
make deploy  IMG=" quay.io/talat_shaheen0/ansible-vmstate-operator:latest"
kubectl create secret generic aws-secret --from-literal=aws-access-key-id=<key> --from-literal=aws-secret-access-key=<key> --from-literal=aws-default-region="us-east-1" -n ansible-vmstate-operator-system;
kubectl apply -f config/samples/aws_v1_awsmanager.yaml -n ansible-vmstate-operator-system; 
kubectl apply -f config/samples/aws_v1_awsec2.yaml -n ansible-vmstate-operator-system
kubectl patch crd  awsec2s.aws.xyzcompany.com -p '{"metadata":{"finalizers":[]}}' --type=merge
make undeploy
