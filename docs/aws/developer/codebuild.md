# buildspec.yml

```yaml

--- 
version: 0.2 
 
phases: 
  install: 
    runtime-versions: 
      python: 3.7 
    commands: 
      - echo "Installing HashiCorp Packer..." 
      - curl -qL -o packer.zip https://releases.hashicorp.com/packer/1.4.2/packer_1.4.2_linux_amd64.zip && unzip packer.zip 
  pre_build: 
      commands: 
        - echo "Validating Packer Template" 
        - ./packer validate packer.json 
  build: 
    commands: 
      - ./packer build -color=false -var 'subnet_id=subnet-0cbb310334e31c9c1' packer.json | tee build.log 
  post_build: 
    commands: 
      - egrep "${AWS_REGION}\:\sami\-" build.log | cut -d' ' -f2 > ami_id.txt 
      # Packer doesn't return non-zero status; we must do that if Packer build failed 
      - test -s ami_id.txt || exit  
       
      # Create Event to CloudWatch 
  #    - sed -i.bak "s/<<AMI-ID>>/$(cat ami_id.txt)/g" ami_builder_event.json 
  #    - aws events put-events --entries file://ami_builder_event.json 
    
      - echo "build completed on `date`" 
      
artifacts: 
  files: 
    - ami_builder_event.json 
    - build.log 
  discard-paths: yes 
```