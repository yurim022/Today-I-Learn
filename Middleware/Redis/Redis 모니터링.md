# Redis 모니터링

</br>

Redis는 많은 트래픽을 처리할 수 있기 때문에 Redis에 문제가 생기면 전체 성능 저하로 이어질 수 있다.    
만약 장비가 fail over 된 상황이면 장비만 고치거나 교환하면 되니 문제가 없지만, 사용패턴에 이슈가 있다면 지속적인 장애가 발생할 수 있다. 
때문에 Redis 장애상황에 대비/대응하기 위해 모니터링은 필수!   

## Redis Metrics

> info all 명령으로 수집할 수 있는 Redis 자체 Metrics

![image](https://user-images.githubusercontent.com/45115557/215080008-2c1013fa-837d-4350-abb1-817d9b44d529.png)



참고링크:   
https://www.datadoghq.com/blog/how-to-collect-redis-metrics/
