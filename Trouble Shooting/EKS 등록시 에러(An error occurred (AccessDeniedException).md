# EKS 등록시 에러
다음 에러는, 아이디가 잘못되어있는 경우. (kbcapital_01)
- $ aws eks update-kubeconfig --name accu-trial                                                          
- An error occurred (AccessDeniedException) when calling the DescribeCluster operation: User: arn:aws:iam::702265188463:user/kbcapital_01 is not authorized to perform: eks:DescribeCluster on resource: arn:aws:eks:ap-northeast-2:702265188463:cluster/accu-trial

# 현재 aws 계정이 어떤걸로 되어있는지 항상 잘 확인하세요
$ aws sts get-caller-identity