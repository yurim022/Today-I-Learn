# ReadinessProbe 와 LivenessProbe

진행중인 프로젝트에서 상용환경에 구성해야할 요구사항이 있었다. Deployments의 인자 중에  `livenessProbe` 와 `readinessProbe` 가 헷갈릴 수 있어서 정리해보고자 한다.

```yml
        readinessProbe:
          httpGet:
            path: /health
            port: 80
          periodSeconds: 50
        livenessProbe:
          httpGet:
            path: /
            port: 80
          periodSeconds: 50
```

### ReadinessProbe

Pod가 요청을 처리할 준비가 되었는지 여부를 확인한다. Pod가 Probe를 실패하면 쿠버네티스는 **Pod를 서비스에서 제외하고 다른 pod로 요청을 전달**한다.   
즉, 서비스와 로드밸런서 등에서 제외한다. 
이를 통해 요청을 처리할 준비가 되지 않은 pod에 대한 요청을 방지할 수 있다. 

### LivenessProbe

이 옵션은 Pod가 살아있는지 여부를 확인한다. Pod가 이 Probe를 실패하면 **쿠버네티스는 Pod를 삭제하고 다시 시작한다.**    
이를 통해 시스템의 다른 부분에 문제를 일으키지 않고 문제가 있는 컨테이너를 다시 시작 할 수 있다. 

</br></br>

## 두 옵션중에 하나만 사용하면 되지 않나?

결론적으로 말하자면 **둘다 사용하는 것이 좋다.**     
예를 들어, 어플리케이션에 문제가 있는 상황에서 `livenessProbe` 만 사용한다면 pod는 계속해서 재실행 되고, 일시적으로 READY가 되었을때
요청이 들어올 수 있다. 당연하게도 이 요청은 제대로 처리되지 못한다. (restart n / ready 1)      

반면  `ReadinessProbe`만 설정되었을 경우, pod는 서비스에서 제외되어 요청이 오지 않지만 재실행 되지 않는다. (restart 0 / ready 0)   

따라서 이 둘을 모두 사용해야 정상적으로 동작하지 않는 pod에 대한 접근을 막으면서 재기동 시킬 수 있다. 


