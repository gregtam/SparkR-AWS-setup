# sparkr-aws-setup

This repo explains how to set up SparkR on AWS. We will use Amazon Elastic MapReduce with Spark and Hive.

The first step is to install the AWS CLI. We can do this by running

```
pip install awscli
```

Before beginning, we will need to login to the [Amazon EC2 dashboard](https://console.aws.amazon.com/ec2/) and create a key pair. We do this by going to the Key Pairs link in the left sidebar, then pressing "Create Key Pair". We enter the name of the key pair and it will download a pem file to your computer. This file is what we use when SSHing. Note that this key pair file is specific to the region indicated at the top right of the page.

Now that we have created a key pair, we can create our EMR cluster through the command line.

```
aws emr create-cluster --applications Name=Hadoop Name=Spark Name=Hive Name=Pig Name=Tez Name=Ganglia \
--release-label emr-5.2.0 --name "EMR 5.2.0 RStudio + sparklyr" --service-role EMR_DefaultRole \
--instance-groups InstanceGroupType=MASTER,InstanceCount=1,InstanceType=m3.xlarge \
InstanceGroupType=CORE,InstanceCount=5,InstanceType=m3.xlarge --bootstrap-actions \
Path=s3://aws-bigdata-blog/artifacts/aws-blog-emr-rstudio-sparklyr/rstudio_sparklyr_emr5.sh,\
Args=["--rstudio","--sparkr","--rexamples","--plyrmr","--rhdfs","--sparklyr"],\
Name="Install RStudio" --ec2-attributes InstanceProfile=EMR_EC2_DefaultRole,KeyName=<key_pair_name> \
--configurations '[{"Classification":"spark","Properties":{"maximizeResourceAllocation":"true"}}]' \
--region <region_name>
```

This command will create an EMR cluster with 1 master node and 5 worker nodes. Note that we will need to fill in `key_pair_name` and `region_name` with the desired key pair and region. We should fill in `key_pair_name` with the name of the key pair as shown in the EC2 console and not the name of the pem file that is on your computer.

Now we are almost set up to use SparkR. All that's left to do is to enable SSH access. We can do this by going to Security Groups in the EC2 console. Under the master node, we click the Inbound tab found at the bottom. Next click Edit and we can add a rule. We would like one for SSH (Port 22) and another Custom TCP Rule for port 8787 (RStudio Server). We can set the source to customer, anywhere, or my IP.

Once we have completed this, we can go to our master node with the port 8787 (`ec2-xxx-xx-xx-xxx.compute-1.amazonaws.com:8787`) in our browser.




