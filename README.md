# AWS virtual network

## Contents

1. [Introduce]()
2. [VPC 구축하고 IGW와 연결하기]()
3. 



## 1. Introduce  

AWS로 가상 네트워크 구축하기

![image](image01.png)  

물리적으로 네트워크를 구성하려면 위와 같이 구성한다.  

나이브하게 대응시키면  

- PC = EC2
- Switch = 가상 스위치(AWS에서는 subnet이 대신함)
- Router = Route table
- Serial Port = Internet Gateway
- NAT = NAT Gateway, NAT Instance

인터넷(Public Domain)과 논리적으로 구분되어, 계정마다의 가상 네트워크를 만드는 것을 Virtual Private Cloud(VPC)라고 한다. 

![image](image02.png)  

약간 이런느낌? 실제로 라우터가 있는건 아니고, 개념적인 라우터랄까.. 모두 라우팅 테이블이라고 보면 된다. VPC에 기본적으로 탑재되어 있는 라우팅 테이블이 있는데 서브넷 별로 라우팅 테이블을 적용시킬 수 있다. 예를 들어 Private Subnet의 경우 외부->내부는 접근하지 못하지만(포트포워딩을 하지 않는 이상) 내부->외부는 가능하도록 Routing table을 NAT Gateway에 물린다. (0.0.0.0은 NAT table로 가라는 식)  

실제로 워크플로우는 Route53 -> IGW(인터넷 게이트웨이) -> VPC -> ELB(로드밸런서, AZ에 하나씩 들어있음) -> Subnet -> EC2 느낌이다.  

그럼 위의 그림처럼 구현해보자(EC2는 하나씩만..)  

## 2. VPC 구축하고 IGW와 연결하기

