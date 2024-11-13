---
title : "Profiling using Nvidia Nsight Tools "



excerpt: "Profiling with Nvidia Nsight tools"

# categories:
#   - 
tags:
  - [deep learning]
# classes : wide
toc: true
toc_sticky: true
---


## Why Profiling is important?
Profiling은 소프트웨어의 **성능을 분석**하기 위한 중요한 기법입니다. 프로그램이 실행되는 동안 CPU 사용량, 메모리 소비, GPU 사용률, I/O 성능 등을 측정하여 병목 현상을 파악하는 데 사용됩니다. 특히 **딥러닝** 및 **병렬 컴퓨팅** 환경에서는 모델의 연산 성능을 최대화하기 위해 필수적인 과정이 됩니다.
최근 들어 **초거대 모델 (Large Language Models, LLM)** 이 인기를 끌면서 연산 비용과 리소스 사용 최적화의 중요성이 더욱 강조되고 있습니다. 수십억 개의 파라미터를 가진 이들 모델은 학습과 추론에서 막대한 GPU 메모리와 연산 자원을 요구합니다.
하드웨어와 소프트웨어 간의 상호작용을 최적화하여 성능을 극대화하는 **시스템 레벨 최적화 (System-Level Optimization)** 이 필요합니다.
특히 , 딥러닝에서는 단일 GPU 활용 뿐만 아니라 GPU 클러스터와 **분산 학습 (Distributed Training)**이 일반화되면서 컴퓨팅 리소스를 효율적으로 사용하는 **병렬 컴퓨팅 (Parallel Computing)** 의 중요합니다.

이처럼 복잡하고 자원이 많이 드는 환경에서는 성능 병목을 정확히 파악하고 개선하기 위해 **프로파일링**이 중요한 역할을 합니다.

---

시간이 점점 지나면서, academy나 industry에서는 단일 GPU를 넘어서서 GPU 클러스터를 구성하여 **분산 학습 (Distributed Training)** 를 통해 학습하거나 추론을 하는 경우가 빈번해지고 있습니다. 
이에 따라 ,   **병렬 컴퓨팅 (Parallel Computing)**  을 최적화하는 기술이 점점 더 요구되고 있습니다.

### The Importance of Parallel Computing and Optimization

병렬 컴퓨팅은 현대 컴퓨터 과학에서 필수적인 요소로 자리 잡고 있습니다. 특히 **딥러닝** 분야에서는 방대한 양의 데이터와 연산을 처리하기 위해 병렬 컴퓨팅 기술이 필수적입니다. 다수의 GPU를 사용하는 **분산 학습 (Distributed Training)** 이 일반화되면서, 연산 속도를 높이고 처리 시간을 단축시키기 위해 병렬화와 최적화는 더욱 중요해졌습니다.

최근 들어 **초거대 언어 모델 (Large Language Models, LLMs)** 및 **초거대 멀티모달 모델 (Large Multi-modal Models, LMMs)**의 발전은 **병렬 컴퓨팅 (Parallel Computing)** 의 중요성을 더욱 부각시키고 있습니다. 이러한 모델들은 수백 GB에 달하는 파라미터를 학습하고, 실시간 추론 시에도 높은 연산 성능을 요구합니다.

**초거대 모델**의 학습은 GPU 클러스터에서 진행이 되고 , **학습 시간 단축**을 위해 병렬화된 연산이 필요합니다. 따라서 ,  **Data Parallelism** ,  **Model Parallelism** 그리고 **Tensor Parallelism** 기술은 학습 효율성을 높이기 위해 널리 사용되고 있습니다. 이러한 **하드웨어 리소스 최적화**는 높은 연산 비용을 줄이고, 더 빠른 추론 성능을 제공합니다.

이러한 복잡한 환경에서는 연산 성능을 극대화하기 위해 최적화 전략이 필요합니다. 하지만 ,  문제는 하드웨어 자원이 증가하면서 성능 병목도 더욱 분산되고 복잡해진다는 점입니다. GPU 간의 **비효율적인 통신**, **메모리 대역폭 제한**, 그리고 **불균형한 작업 분배** 등이 주요한 병목 요소로 작용할 수 있습니다.

---
이러한 **병렬 컴퓨팅 (Parallel Computing)**의 최적화를 하기 위해서는 다양한 metric을 수집해야 합니다. 하지만, 개발자나 연구자가 이러한 툴을 직접 만들어서 사용하는 것은 비효율적입니다. 따라서 , 이러한 Metric 수집을 가능하게 하는 프로파일링 도구인 **Nvidia Nsight Tools**와 **PyTorch Profiler** 가 있습니다.

### Profiling Tools : Nvidia Nsight Tools ,Pytorch Profiler


복잡한 딥러닝 모델과 대규모 컴퓨팅 환경에서는 성능을 최적화하기 위해 **정확한 프로파일링 도구**가 필수적입니다. 프로파일링 도구는 코드의 성능을 분석하고, 리소스 사용 현황을 파악하여 병목 현상을 진단할 수 있는 유용한 방법을 제공합니다. 특히, **GPU 기반 연산**에서의 최적화는 모델의 학습 속도와 추론 성능에 직결되기 때문에 정확한 성능 분석이 필요합니다.

최근 딥러닝의 발전과 함께 **Nvidia Nsight Tools**와 **PyTorch Profiler** 같은 강력한 프로파일링 도구가 널리 사용되고 있습니다. 이들은 GPU 사용률, 메모리 소비, 커널 실행 시간 등의 주요 지표를 실시간으로 모니터링할 수 있게 해줍니다. 그러나, 문제는 대규모 모델과 복잡한 연산이 병렬로 수행될 때 성능 병목이 더 불분명하고 진단하기 어려워진다는 점입니다.

- **Nvidia Nsight Tools**는 GPU 성능 분석에 특화된 도구로, CUDA 커널 실행 시간, 메모리 전송, GPU 간 통신 등의 다양한 메트릭을 시각화할 수 있습니다. 이를 통해 GPU의 사용 효율성을 극대화할 수 있습니다.
- **PyTorch Profiler**는 딥러닝 모델의 연산 성능을 분석하고, 시간 소모가 많은 연산(`hot spots`)을 파악하여 모델의 병목을 제거할 수 있도록 도와줍니다. PyTorch의 기본 프로파일링 기능과 함께 TensorBoard를 통한 시각화도 지원합니다.

---

**Nvidia Nsight Tools**와 **PyTorch Profiler** 모두 Profiling Tool 입니다. 하지만, 각 상황마다 유용한 Tool은 다를 것입니다. 

#### PyTorch Profiler vs. Nvidia Nsight Tools: 차이점

두 도구는 성능 분석을 위해 사용되지만, **대상**과 **제공 기능**에서 차이가 있습니다.

1. **분석 대상**
   - **PyTorch Profiler**는 **PyTorch 프레임워크** 내에서 실행되는 연산을 주로 분석합니다. 딥러닝 모델의 레이어별 연산 시간, CPU와 GPU 간의 작업 분포, 데이터 로딩 시간을 추적할 수 있습니다.
   - **Nvidia Nsight Tools**는 보다 **저수준의 하드웨어 성능 분석**을 제공합니다. CUDA 커널, 메모리 전송, GPU 간 통신 등 GPU의 전체적인 사용률과 성능을 모니터링할 수 있어, PyTorch 외의 다양한 CUDA 애플리케이션에도 사용 가능합니다.

2. **사용자 대상**
   - **PyTorch Profiler**는 **딥러닝 연구자**나 **모델 개발자**에게 적합합니다. 주로 딥러닝 모델의 성능 문제를 해결하고 최적화하는 데 초점을 맞추고 있습니다.
   - **Nvidia Nsight Tools**는 **시스템 엔지니어**나 **GPU 최적화 전문가**에게 더 적합합니다. 하드웨어와 CUDA 코드의 성능을 세밀하게 분석할 수 있어, GPU 리소스를 최대한 활용하고자 하는 경우 유리합니다.

3. **시각화 및 분석 기능**
   - **PyTorch Profiler**는 **TensorBoard**와의 연동을 통해 시각적인 분석을 쉽게 할 수 있습니다. 직관적인 그래프와 히트맵을 통해 연산 병목(`bottlenecks`)을 빠르게 식별할 수 있습니다.
   - **Nvidia Nsight Systems/Nsight Compute**는 **세부적인 GPU 메트릭과 타임라인 분석** 기능을 제공합니다. CUDA 스트림, 메모리 사용 패턴, 커널 실행 타임라인 등을 상세하게 시각화할 수 있어 하드웨어 수준에서의 문제 진단이 가능합니다.

4. **지원 환경**
   - **PyTorch Profiler**는 PyTorch 프레임워크에 최적화되어 있으며, CPU와 GPU를 모두 지원합니다.
   - **Nvidia Nsight Tools**는 Nvidia GPU에 특화되어 있으며, CUDA 애플리케이션과의 연동을 통해 보다 깊이 있는 분석이 가능합니다.

---

이처럼 **PyTorch Profiler**와 **Nvidia Nsight Tools**는 각각의 장점과 특화된 기능이 있으며, **사용 목적과 분석 대상에 따라 선택**하는 것이 좋습니다. 

## Overview of Nvidia Nsight Tools

![Nvidia_Gpu_Profiler_-_Layered_Architecture](https://github.com/user-attachments/assets/82e96014-7625-4c7c-b4ce-b56bb6f23785)  



GPU의 성능을 최대한 활용하기 위해 **정확한 성능 분석**이 필요합니다. Nvidia는 이를 위해 **Nsight Tools**라는 강력한 프로파일링 도구를 제공합니다. Nsight Tools는 GPU 하드웨어와 CUDA 애플리케이션의 성능을 상세하게 분석할 수 있어, 병렬 컴퓨팅과 최적화 작업에 필수적인 도구로 자리 잡았습니다.

GPU에서 딥러닝 모델을 Training 하거나 Inference를 할 때 다음과 같은 **성능 저하** 현상을 보일 수 있습니다.

- 최근의 딥러닝 모델은 매우 복잡한 연산을 포함하고 있습니다. 연산 시간이 길어지고, 리소스가 **비효율적으로 사용**되기 때문에 비용이 크게 증가할 수 있습니다.
- 복잡한 CUDA 커널과 GPU 메모리 전송 간의 **비효율적인 연산**이 병목을 일으킬 수 있습니다.
- 여러 GPU 간의 **통신 지연** 문제는 분산 학습에서의 성능 저하로 이어질 수 있습니다.
- 하드웨어 자원 사용이 최적화되지 않으면 성능 저하뿐만 아니라 **전력 소비**도 증가할 수 있습니다.

이러한 복잡한 성능 문제를 해결하기 위해 Nvidia Nsight Tools는 다음과 같은 기능을 제공합니다:

- **Nsight Systems**: 전체 애플리케이션의 성능을 프로파일링하고, CPU와 GPU 간의 상호작용 및 스레드 활동을 분석합니다. 이를 통해 시스템 수준의 성능 병목을 파악할 수 있습니다.
- **Nsight Compute**: CUDA 커널의 세부적인 성능 메트릭을 분석하고, 메모리 사용 패턴과 연산 효율성을 파악할 수 있는 도구입니다. 이를 통해 GPU에서 발생하는 저수준의 성능 문제를 진단할 수 있습니다.

그렇다면, **왜 Nvidia Nsight Tools가 중요한 것일까요?**

1. **시스템 및 커널 수준의 통합 분석**: Nsight Systems와 Nsight Compute는 각각 **시스템 전체**와 **커널 세부 성능**을 분석할 수 있어, 병목 문제를 다각도로 진단할 수 있습니다.
2. **정밀한 성능 메트릭 제공**: 메모리 사용, GPU 활용도, 커널 실행 시간 등의 다양한 메트릭을 통해 최적화에 필요한 데이터를 제공합니다.
3. **GPU 최적화 지원**: GPU 리소스의 활용도를 높여, 연산 효율성을 극대화하고 비용 절감을 가능하게 합니다.
4. **확장성 있는 분석 기능**: 단일 GPU에서 다중 GPU, 그리고 클러스터 환경까지 다양한 규모에서 성능 분석이 가능합니다.



---
Nvidia Nsight Tools를 사용하는 rule of Thumb은 다음과 같은 순서를 따릅니다. 
1. Nvidia Nsight Systems를 통해서 딥러닝의 모듈의 bottleneck을 분석합니다. 
2. bottleneck인 모듈을 Nsight Compute를 통해 GPU device 관점에서 분석을 합니다. 

다음으로, **Nsight Systems**와 **Nsight Compute**의 세부 기능을 살펴보겠습니다.

### Nsight Systems

딥러닝 모델에서 성능 문제를 해결하려면, 시스템 전체의 연산 흐름과 자원 사용 현황을 분석하는 것이 중요합니다. **Nsight Systems**는 Nvidia가 제공하는 강력한 프로파일링 도구로, **애플리케이션의 시스템 수준 성능**을 분석하고, CPU와 GPU 간의 상호작용을 파악할 수 있도록 도와줍니다. 특히, CPU와 GPU의 비동기 연산이 많은 환경에서는 작업 스케줄링과 메모리 전송의 병목 현상이 성능 저하의 원인이 될 수 있습니다.

최근 들어, **대규모 딥러닝 모델**을 사용시에  **분산 컴퓨팅 환경**이 증가하면서 시스템 수준의 성능 문제는 더욱 복잡해지고 있습니다. 단순히 GPU의 사용률만을 모니터링하는 것으로는 충분하지 않습니다. 다음과 같은 요소들이 복합적으로 작용하여 성능 병목을 일으킬 수 있기 때문입니다.
- **비효율적인 CPU-GPU 동기화**는 연산의 병목을 초래할 수 있습니다.
- **데이터 로딩 지연**은 모델 학습 속도를 저하시킬 수 있으며, 특히 I/O 병목 문제는 대규모 데이터셋에서 두드러집니다.
- **GPU 간 통신 병목**은 분산 학습에서 성능 저하로 이어질 수 있습니다.


Nsight Systems는 단순한 하드웨어 수준 분석을 넘어, **딥러닝 프레임워크에서 정의한 논리적인 모듈 단위로도 분석**할 수 있는 기능을 제공합니다. 예를 들어, PyTorch의 **Module** 또는 TensorFlow의 **Layer** 단위로 성능을 분석할 수 있어, 모델의 각 레이어나 블록에서 발생하는 병목을 쉽게 파악할 수 있습니다. 이를 통해 사용자는 더 세부적인 성능 최적화가 가능합니다. **모듈 단위 분석**을 통해, 연산 시간, 메모리 사용량 등을 레이어별로 확인할 수 있어 병목 현상이 발생하는 특정 모듈을 빠르게 식별할 수 있습니다. 이러한 기능은 복잡한 모델의 구조를 이해하고, 레이어별 최적화 전략을 수립하는 데 큰 도움을 줍니다.

**Nsight Systems** 에서 Profiling을 할 수 있는 범위는 다음과 같습니다. 

1. **시스템 전체의 타임라인 분석**: CPU, GPU, 메모리 간의 상호작용을 타임라인 형식으로 시각화하여, 병목 현상이 발생하는 지점을 직관적으로 파악할 수 있습니다.
2. **멀티 스레드 분석 기능**: CPU의 스레드 활동과 GPU 커널 실행 간의 관계를 분석하여, 비동기 연산의 문제를 해결할 수 있습니다.
3. **I/O 성능 최적화 지원**: 디스크 I/O, 네트워크 전송 등의 성능을 분석하여, 데이터 로딩과 통신의 병목을 줄일 수 있습니다.
4. **모듈 단위 분석**: 딥러닝 프레임워크의 레이어나 모듈 단위로 성능을 분석할 수 있어, 모델 구조에서 발생하는 병목 현상을 더 세부적으로 진단할 수 있습니다.
5. **확장성 있는 분석 기능**: 단일 GPU에서 다중 GPU, 그리고 클러스터 환경까지 다양한 규모에서 성능 분석이 가능합니다.


---
Nsight System 으로 **시스템 레벨** 에서의 최적화를 시도할 수 있습니다. 그렇다면, Nsight Compute로 **커널 레벨** 에서의 Profiling을 해야하는 이유는 무엇일까요?  

### Nsight Compute

딥러닝 모델에서 GPU의 성능을 극대화하려면, GPU 내부에서 실행되는 **CUDA 커널**의 성능을 세밀하게 분석해야 합니다. **Nsight Compute**는 Nvidia가 제공하는 고급 프로파일링 도구로, CUDA 커널의 **세부 성능 메트릭**을 수집하고 분석하여 GPU 최적화 작업을 지원합니다. 이를 통해 사용자는 커널 코드의 비효율적인 부분을 찾아내고, 성능을 개선할 수 있습니다.

딥러닝과 같은 대규모 연산 환경에서는 CUDA 커널의 성능 문제로 인해 예상보다 낮은 GPU 활용률을 보일 수 있습니다. 특히 **복잡한 행렬 연산**이나 **메모리 접근 패턴**이 비효율적일 경우, GPU의 계산 성능이 제대로 발휘되지 않을 수 있습니다. GPU 성능 저하를 초래하는 원인은 다음과 같습니다. 

- **메모리 대역폭 병목**: 비효율적인 메모리 접근은 연산의 병목 현상을 초래할 수 있습니다.
- **CUDA 커널 비효율성**: 커널의 비효율적인 코드 구조는 실행 시간을 증가시키고, GPU 자원의 활용을 저해할 수 있습니다.
- **리소스 경합**: 여러 커널이 동시에 실행되면서 리소스 경합이 발생할 수 있으며, 이는 전체 연산 속도에 부정적인 영향을 미칩니다.

이러한 문제들은 GPU 내부에서만 발생하는 저수준의 문제이기 때문에, 단순한 시스템 수준의 프로파일링 도구로는 감지하기 어렵습니다. 이를 해결하기 위해 Nsight Compute는 다음과 같은 기능을 제공합니다.


1. **세부적인 커널 분석**: CUDA 커널의 실행 시간, 메모리 사용 패턴, 연산 효율성을 상세히 분석할 수 있습니다. 이를 통해 커널의 비효율적인 코드 구조를 쉽게 파악할 수 있습니다.
2. **고급 메트릭 제공**: 메모리 대역폭, 연산 강도(Arithmetic Intensity), 스레드 다이버전스(Thread Divergence) 등의 고급 메트릭을 통해 GPU 성능 저하의 원인을 진단할 수 있습니다.
3. **실시간 분석 및 최적화 제안**: Nsight Compute는 사용자가 분석한 데이터를 바탕으로 최적화 제안을 제공하여, 코드 개선 방향을 제시합니다.
4. **자동화된 리포트 생성**: 분석 결과를 기반으로 한 상세한 리포트를 자동 생성하여, 최적화 작업에 필요한 정보를 한눈에 확인할 수 있습니다.
5. **프레임워크와의 통합 분석**: PyTorch, TensorFlow 등의 딥러닝 프레임워크에서 호출된 CUDA 커널도 분석할 수 있어, 모델의 특정 연산 단계에서 발생하는 문제를 파악할 수 있습니다.

---

## Setting Profiling Environment 


프로파일링 **환경을 설정**하는 것은 모델 최적화 과정에서 첫 번째 단계로, 성능 문제를 정확히 분석하고 해결하기 위한 필수적인 준비 작업입니다. 특히 대규모 딥러닝 모델을 다룰 때, **하드웨어**와 **소프트웨어 환경**의 설정이 중요합니다. 특히, Transformer architecture를 기반으로하는 모델의 복잡한 작업을 실시간으로 수행하기 위해 고도의 최적화가 필요하며, 이를 위해 성능 프로파일링이 핵심적인 역할을 합니다.

Transformer architecture를 기반의 복잡한 모델을 다룰 때, 모델이 사용하는 하드웨어와 소프트웨어 환경이 성능 분석의 정확성과 효율성에 영향을 미칠 수 있습니다. 다양한 하드웨어 리소스(CPU, GPU, 메모리 등)를 사용할 경우, 각 리소스의 사용을 모니터링하고 병목 현상을 정확히 식별하는 것이 어려울 수 있습니다. 또한, 프로파일링 도구가 제공하는 데이터는 매우 방대하여 이를 해석하고 실제 최적화 방안을 도출하는 과정이 복잡해집니다.

그렇다면, 모델을 최적화하기 위한 프로파일링 환경을 어떻게 설정할 수 있을까요? 모델의 성능을 극대화하기 위해 필요한 하드웨어와 소프트웨어 구성은 무엇이며, 각 요소를 어떻게 최적화할 수 있을까요?



### Hardware and Software

Nvidia Nsight 도구를 활용하여 딥러닝 모델의 성능을 효율적으로 프로파일링하고 최적화하려면, 적합한 하드웨어 및 소프트웨어 환경 설정이 필수적입니다. 특히, Nvidia Nsight 도구는 GPU 자원의 효율적 활용과 메모리 및 네트워크 성능 분석을 위해 고도의 시스템 사양을 요구하므로, 이를 위한 환경을 제대로 구축하는 것이 중요합니다. 다음은 Nvidia Nsight 도구 사용을 위한 시스템 요구 사항, 지원되는 플랫폼, CUDA 버전, 호스트 애플리케이션 요구 사항을 설정하는 가이드입니다.

Nvidia Nsight 도구가 원활히 실행되기 위한 기본 시스템 요구 사항은 지원되는 **Nvidia GPU 아키텍처**를 갖추는 것입니다. 분석 도구가 실행되는 동안 대량의 데이터를 처리하므로, 8코어 이상의 CPU와 32GB 이상의 메모리를 갖춘 환경이 추천됩니다.

지원되는 플랫폼은 **Linux (Ubuntu 20.04 이상)** 가 가장 적합하며, Windows 환경도 지원됩니다. 하지만, GPU 가속과 CUDA의 최적화를 위해 Linux 기반 환경이 더 유리합니다. CUDA 버전은 **최소 11.3 이상**(Nsight version에 따라 상이함) 을 요구하며, 최신 버전을 유지할수록 GPU 활용과 Nsight의 성능 분석 결과의 정확도가 높아집니다. CUDA 및 GPU 드라이버는 Nsight 도구와의 호환성을 위해 항상 최신 상태로 유지하는 것이 좋습니다.

호스트 애플리케이션 환경은 **Python 3.8 이상**(Pytorch,Cuda등의 호환성은 version 에 따라 상이함.)을 지원하며, Nsight를 통해 모델 성능을 분석할 경우, CUDA 호환성을 갖춘 PyTorch 또는 TensorFlow 등의 프레임워크를 설치하여 GPU 활용을 최적화할 수 있습니다. 추가로 TensorRT와 같은 고성능 추론 라이브러리를 설치하면 딥러닝 모델의 추론 속도와 성능 최적화에 더욱 효과적입니다.

Nsight 도구를 위한 환경을 설정할 때, 먼저 GPU와 CUDA에 맞는 패키지 구성을 확인해야 합니다. Nvidia의 공식 사이트에서 CUDA 버전에 호환되는 Nsight 도구와 기타 필수 패키지를 확인하고 설치하는 것이 좋습니다. Nvidia Nsight Reference에 적힌대로 설치를 하시면 편하게 pacakge 설치를 할 수 있고, 다양한 호환성을 만족시킬 수 있습니다. 

패키지 관리자를 통해 필수적인 종속성과 패키지를 효율적으로 설치하여, Nsight 도구의 성능 분석 및 최적화를 위한 환경을 준비할 수 있습니다. Conda 명령어를 통해 필요 패키지의 버전과 종속성을 관리함으로써, Nvidia Nsight 도구가 최적의 성능을 발휘할 수 있는 환경을 구축할 수 있습니다.


Nvidia Nsight Systems를 위한 environment를 설정하는 [링크](!https://docs.nvidia.com/nsight-systems/InstallationGuide/index.html#) 와 Nvidia Nsight Compute의 environment를 설정하는 [링크](!https://docs.nvidia.com/datacenter/tesla/driver-installation-guide/contents.html) 입니다. Nvidia Nsight Compute의 경우 공식적인 Installation Guide가 따로 없이 Nvidia Driver와 Cuda Driver를 설치하면 함께 설치됩니다. Nvidia Nsight Compute의 경우 **Introduction** , **Pre-installation Actions** , **Package Manager Installation** ,**Driver Installation** ,**Post-installation Actions** 를 참고하여 설치하면 문제없이 작동이 됩니다. 

이때 , Ubuntu의 오래된 version에 설치하는 것이 좀 더 안정적인거 같습니다. 또한, CUDA Driver를 GPU와 호환이 되는 version을 확인하여 설치하는 것이 중요합니다 .뿐만 아니라 , 설치하고자 하는 Nsight Tool과 Cuda Driver가 호환이 되는지를 잘 체크하여야 합니다. **Driver Installation** 은 default로 최신 version을 설치하게 됩니다. 따라서,특정  version을 잘 찾아서 설치하는 것이 중요합니다.

**환경 설정** 을 완료했으면 분석을 할 **딥러닝 모델**이 필요합니다. 


### Model : RT-DETR

Nvidia Nsight 도구를 통해 성능 분석을 수행할 모델로 **RT-DETR**을 선택했습니다. RT-DETR은 **실시간 객체 탐지(Real-Time Object Detection)** 모델로, 기존 객체 탐지 모델에 비해 높은 효율성과 정확도를 제공합니다. 이는 디지털 이미지나 비디오에서 사물을 탐지하는 작업에 최적화된 모델로, 최신 트랜스포머 아키텍처를 적용해 성능을 크게 개선한 것이 특징입니다. 

RT-DETR은 여러 단계의 피처 추출 및 트랜스포머 기반의 **DETR** 구조를 활용하여 복잡한 장면에서도 빠르고 정확한 탐지를 수행합니다. 이러한 특성 덕분에 실시간 객체 탐지가 요구되는 자율 주행, 실시간 영상 분석 등 다양한 응용 분야에서 널리 활용되고 있습니다.

RT-DETR 모델을 성능 분석의 대상으로 선택함으로써, 실시간 환경에서의 객체 탐지 성능을 최적화하는 데 필요한 정보와 병목 지점을 파악할 수 있습니다. 이는 최적화할 수 있는 주요 요소들을 이해하고, 모델의 응답성을 높이는 데 기여할 수 있는 유용한 정보를 제공합니다. RT-DETR 모델에 대한 Nvidia Nsight 도구의 프로파일링은 다음의 몇 가지 중요한 질문으로 구체화할 수 있습니다.

1. **RT-DETR의 연산 병목 지점은 어디인가?**  
2. **모델의 레이턴시와 메모리 사용량을 최적화하려면 어떤 요소를 조정해야 하는가?**  
3. **실시간 객체 탐지 성능을 극대화하기 위해 Nsight 도구에서 중점적으로 분석해야 할 부분은 무엇인가?**

이러한 질문을 바탕으로 Nvidia Nsight 도구를 활용해 RT-DETR 모델의 성능을 체계적으로 분석할 수 있으며, 이를 통해 **실시간 객체 탐지(Real-Time Object Detection)** 에서 보다 최적화된 모델을 구현할 수 있을 것입니다.

**환경 설정** 과 **딥러닝 모델** 이 준비가 되었으므로 본격적으로 **Nvidia Nsight Tools**를 활용해서 Profiling을 시작할 수 있습니다. 

## Profiling Using Nsight Systmes

프로파일링은 모델의 성능 최적화에 필수적인 단계입니다. 특히 **Nvidia Nsight Systems**는 GPU와 CPU의 연산 처리 흐름을 시각화하여 모델의 연산 병목을 파악하고, 성능 최적화를 위한 주요 지표를 제공합니다. 이를 통해 모델이 실제 운영 환경에서 필요한 성능을 발휘할 수 있도록 최적화하는 데 도움을 줍니다.

딥러닝 모델을 최적화할 때, CPU와 GPU 간의 연산 타이밍을 조율하거나 메모리 대역폭을 개선하는 것이 중요합니다. 그러나 실시간으로 많은 연산을 처리하는 모델에서는 예상치 못한 병목 현상이 자주 발생합니다. Nsight Systems는 이와 같은 문제점을 깊이 있게 분석하여 최적화에 필요한 구체적인 정보를 제공합니다. 

현재 사용 중인 모델에 대해 Nvidia Nsight Systems를 통해 프로파일링을 진행함으로써, 성능 향상에 필요한 중요한 질문들을 명확히 할 수 있습니다. 이러한 질문들은 다음과 같습니다:

1. **CPU와 GPU의 비효율적인 동작 지점은 어디인가?**  
   모델의 병목 현상이 CPU나 GPU의 특정 구간에서 발생하는지 파악하고, 이에 따라 자원 활용을 최적화할 필요가 있습니다.

2. **메모리 대역폭과 메모리 사용량을 어떻게 개선할 수 있는가?**  
   메모리 병목은 모델의 처리 속도를 크게 저하시킬 수 있습니다. Nsight Systems는 메모리 대역폭 및 사용량을 모니터링하고, 이에 따라 최적화할 수 있는 기회를 제공합니다.

3. **모델 레이턴시를 줄이기 위해 최적화할 수 있는 지점은 어디인가?**  
   실시간 환경에서 낮은 레이턴시는 매우 중요한 요소입니다. Nsight Systems를 활용해 모델의 각 연산 단계에서 레이턴시를 분석하고, 개선 방안을 모색할 수 있습니다.

1,2 의 질문들은 주로 대용량의 데이터를 사용하는 Training 단계에서 유용하고 , 3의 질문은 Inference 단게에서 유용합니다. 

이러한 질문을 통해 모델의 성능을 제한하는 요소들을 구체적으로 확인하고, Nvidia Nsight Systems의 심층 분석 결과를 바탕으로 구체적인 최적화 작업을 수행할 수 있습니다. 이를 통해 모델의 실시간 성능을 효과적으로 향상시키고, 더욱 효율적인 시스템을 구현할 수 있습니다.



### How to Setting Nvidia Nsight in Pytorch


![image](https://github.com/user-attachments/assets/a3b65423-dc03-4be1-8809-301e1c8c359c)  


**PyTorch**는 딥러닝 연구와 모델 개발에 널리 사용되는 라이브러리로, 성능 최적화를 위해 GPU 프로파일링이 필수적입니다. Nvidia Nsight는 PyTorch 모델을 실행할 때 발생하는 GPU 연산과 자원 사용에 대한 심층적인 분석을 제공하는 도구입니다. 이를 통해 개발자는 모델 성능 병목을 찾아내고 최적화 작업을 진행할 수 있습니다.

PyTorch 환경에서 모델을 프로파일링하려면, **Nsight를 설정**하는 과정이 필요합니다. 이 과정에서 발생할 수 있는 문제는, PyTorch와 Nsight 간의 통합이 다소 복잡할 수 있다는 점입니다. 특히, PyTorch 코드에서 Nsight를 효과적으로 사용할 수 있도록 환경을 설정하는 데 시간이 소요될 수 있습니다. 이로 인해 실험적인 설정이 필요하고, PyTorch의 다양한 버전과의 호환성 문제도 고려해야 합니다.

다음은 이 과정에서 고려해야 할 주요 질문들입니다:

1. **Nvidia Nsight 도구를 PyTorch 환경에 어떻게 설정할 수 있을까?**
   PyTorch에서 Nsight를 사용하려면, 먼저 Nsight Compute와 Nsight Systems를 설치하고 환경을 설정해야 합니다. 이 과정에서는 PyTorch 코드에 Nsight 툴을 연동할 수 있도록 CUDA 버전 및 PyTorch 버전의 호환성 확인이 필요합니다.

2. **PyTorch 코드 내에서 Nsight를 실행하려면 어떤 설정이 필요한가?**
   Nsight를 실행하려면, PyTorch 코드에서 GPU 연산을 트레이싱하거나 프로파일링하려는 특정 지점에 맞춰 Nsight API나 명령어를 삽입해야 합니다. 또한, 프로파일링을 통해 수집할 메트릭을 선택하고, 이를 분석하기 위한 환경을 구성해야 합니다.


이를 통해, Nvidia Nsight는 PyTorch 모델을 최적화하는 데 중요한 역할을 하며, 모델의 성능을 개선하는 데 필요한 인사이트를 제공합니다. PyTorch와 Nsight의 설정을 잘 이해하고, 이를 바탕으로 효율적인 GPU 자원 활용과 성능 최적화를 할 수 있습니다.

Pytorch에서 Nvidia Nsight를 어떻게 설정하는지 알아보겠습니다. 
```python
    i=0
    warmup_steps = 10 

    for samples, targets in metric_logger.log_every(data_loader, 10, header):

        samples = samples.to(device)
        targets = [{k: v.to(device) for k, v in t.items()} for t in targets]


        if i== warmup_steps :torch.cuda.cudart().cudaProfilerStart()
        if i>= warmup_steps : torch.cuda.nvtx.range_push("iterations{}".format(i))

        # if i>= warmup_steps : torch.cuda.nvtx.range_push("evaluation")
        outputs = model(samples)


        orig_target_sizes = torch.stack([t["orig_size"] for t in targets], dim=0)        
        results = postprocessors(outputs, orig_target_sizes)
        res = {target['image_id'].item(): output for target, output in zip(targets, results)}
        if coco_evaluator is not None:
            coco_evaluator.update(res)

        torch.cuda.nvtx.range_pop()
        i+=1

        if i>=2 :  ## 5 : 1 wait 1 warmup   3 active
            break

    torch.cuda.cudart().cudaProfilerStop()
```
여기서 `torch.cuda.cudart().cudaProfilerStart()` 와 `torch.cuda.cudart().cudaProfilerStop()`는 전체 profiling의 시작과 끝을 정해주는 method입니다. 이 두 method사이의 코드에 대해서만 Nvidia Nsight Tool이 profiling을 할 수 있게 됩니다 . 그 다음은 `torch.cuda.nvtx.range_push` , `torch.cuda.nvtx.range_pop` 입니다.이 method는 **논리적인** range를 **stack frame**의 형태로 설정해줍니다. 따라서, `torch.cuda.nvtx.range_push` 를 호출한 상태에서 추가적으로 `torch.cuda.nvtx.range_push` 를 호출하게 되면 호출된 range를 stack frame에 쌓게 됩니다. 따라서, nested 한 range를 설정이 가능하게 해줍니다.  
Module단위로 pytorch에 range를 설정할려면 hook을 통해서 설정할 수 있습니다.
```python
    for idx, m  in enumerate(module.named_modules()) :
        def pre_forward_hook(name) :
            def prehook_closure(module,input):
                torch.cuda.nvtx.range_push(f"{name}")
            return prehook_closure
        def forward_hook(name) :
            def forward_hook_closure(module,input,output ):
                torch.cuda.nvtx.range_pop()
            return forward_hook_closure
        m[1].register_forward_pre_hook(pre_forward_hook(m[0]))
        m[1].register_forward_hook(forward_hook(m[0]))

```
python의 register_forward_pre_hook, register_forward_hook을 통하여 hook을 설정하였습니다. pytorch의 hook의 definition은 positional argument입니다. 따라서, argument의 역할의 정해져 있기에 일반적인 function의 definition으로는 module의 name state를 추가할 수 없습니다. factory function , 즉 closure 라 불리는 구조를 사용해서 module name의 state를 binding 하였습니다. class를 사용하지 않은 이유는 name이라는 state만 필요할 뿐 inheritance, overriding 같은 기능이 필요없기 때문입니다.

위의 과정을 통해서 pytorch에서의 Nvidia Nsight setting이 끝났습니다. 


### How to do profiling using Nsight Systems
```
nsys profile -w true -t cuda,nvtx,osrt,cudnn,cublas -s cpu  --capture-range=cudaProfilerApi --capture-range-end=stop --cudabacktrace=true -x true -o <output> <application> <app_args>
```
 라는 command를 사용하게 되면 Host-Device Perspective에서 application을 분석할 수 있습니다. command-line의 argument를 분석해보도록 하겠습니다. 
- `-t` 는 `--trace`의 shortcut입니다. 어떤 api를 trace할 것인지 설정합니다.
- `--capture-range` 는 어디서 부터 range를 시작해서 profiling 할 것인지를 설정하는 부분입니다. 이 예시 코드의 경우 `cudaProfilerApi` 를 설정한 부분만 profiling하게 합니다. 이렇게 하는 이유는 cuda api를 호출하지 않는 부분까지 profiling을 하게 되면 비효율적이기 때문입니다. 따라서, 필요한 부분만 profiling을 하기 위해서 설정해줍니다.
- `--capture-range-end` 는 시작한 range의 끝을 정해주는 부분입니다. 이 예시 코드의 경우 `stop`이 나오는 경우 profiling을 비활성화게 됩니다.
- `--cudabacktrace` 는 cudaAPI를 tracing 할 때 backtracing을 수집하게 됩니다. 즉, 어떤 cudaAPI를 호출할 때 연쇄적으로 어떤 api를 호출했는지 tracing 하게 됩니다.
- `-o` 는 profiling 결과를 어떤 이름의 file로 저장할 지 지정해줍니다. 파일의 확장자는 `nsys-rep` 입니다.

이러한 command를 실행하게 되면 `nsys-rep`이라는 확장자의 file이 실행이 됩니다. 이를 Nvidia System의 GUI를 실행시켜서 Open할 수 있습니다.  
![image](https://github.com/user-attachments/assets/35c7effb-56d9-46e8-af9f-1ca03fc25f50)  

파일을 Open하게 되면 다음과 같은 report를 얻게 됩니다.
![image](https://github.com/user-attachments/assets/16ea3cb5-2ad1-48e9-95ac-7993cd0173b6)  

이 때 , profiling시  iteration 구간을 **최대한 짧게** 하는 것이 좋습니다. 왜냐하면, 정확한 수치보다는 **대략적인 경향성**을 파악하는 것이 목적이기 때문입니다. profiling하는 구간이 길수록 overhead가 커지기 때문에 최대한 구간을 짧게 하는 것이 좋습니다. 저는 10번의 iteration 정도만 profiling하였습니다.

위의 report를 보면 `iteration11` 에 대한 분석이 2개가 있음을 확인할 수 있습니다. 하나는 `CUDA HW` 에서의 profiling이고 하나는 `Threads`에서의 profiling입니다. `CUDA HW`의 경우 `Device` 에서 execution을 나타내고 , `Threads`는 `Host`에서 호출후 종료가 되기까지를 나타냅니다. 따라서 ,pytorch에서 `Host`에서 module을 연산을 실행하더라도 이전 module의 결과에 synchronous하기 때문에 `CudaStreamSynchronize`를 호출하는 것을 볼 수 있습니다. 제 개인적으로는 실제 performance에서의 bottleneck을 분석하기 위해서는 `CUDA HW`에서의 profiling 결과를 분석해야 한다고 생각합니다. 실제 `Device` 에서의 performance를 optimization하면 `Host`에서의 performance가 향상되기 때문입니다. 각 range를 더블click 하면 아래의 `Event view`에서 확인할 수 있습니다 .
 이 때 주의할점은 , 한번 range를 click하면 `CUDA HW` 나 `Threads`에서 전환을 하기 위해서는 `Shift + MouseDoubleClick`을 통해야만 상호간의 전환이 가능합니다. 만약에, 잘못 더블click을 하였다면 profiling 하고자하는 perspective를 바꾸는 것이 중요합니다. 

각 Perspective에서 분석할 수 있는 결과는 다음과 같습니다.
- CUDA HW
    - Kernel : GPU에서 어떤 Kernel을 실행하였는지 나타냅니다. 여기에서는 호출된 모든 kernel이 표시됩니다.
    - Memory : H2D , D2H 등의 access를 나타냅니다. 
- Threads
    - cuBLAS : cuBLAS api의 호출을 나타냅니다.
    - CUDA API : cuda API의 호출을 나타냅니다.
    - cuDNN : cuDNN 라이브러리를 호출했는지 나타냅니다 .


Nsight Systems로는 Host , Device의 Level에서 Profiling을 할 수 있습니다. 하지만, GPU 내부의 L1 cache, L2 cache, Device Memory의 metric은 분석할 수 없습니다. 따라서, 이러한 경우 Nsight Compute를 사용하게 됩니다 .

### Nsight Systems Profiling Result
Nsight System에서의 Profiling을 분석하였습니다. 

![image](https://github.com/user-attachments/assets/f38a48c6-2500-430f-904d-2a1c6c52b0f0)  
backbone 부분이 가장 큰 bottleneck임을 알 수 있었습니다. 이번에는 backbone , encoder ,decoder의 level에서 profilng을 분석해보겠습니다.  

- backbone
    - ![image](https://github.com/user-attachments/assets/f82e948c-32a8-4981-8df6-f5b62be3327e)
- encoder
    - ![image](https://github.com/user-attachments/assets/50971020-b16d-4dbb-aa4d-2c3a3a1c13bc)
- decoder
    - ![image](https://github.com/user-attachments/assets/0be6195c-b1ea-494b-a591-7c50c531a824)
    - ![image](https://github.com/user-attachments/assets/66fc220c-8567-44a3-8b54-f9e37e11400f)

Profiling을 한 결과는 다음고 같습니다.
- backbone에서는 각 resnet layer의 첫번째 block이 bottleneck임을 확인하였습니다. 
- encoder에서는 각 fpn block의 bottleneck module과 self-attention layer가 bottleneck임을 확인하였습니다. fpn block중 특히 2번째 fpn block이 bottleneck임을 확인하였습니다. 
- decoder에서는 각 decoder layer의 self-attention과 cross-attention이 bottleneck임을 확인하였습니다. 

이를 통해 중점적으로 분석해야할 kernel들을 명확히 할 수 있게 되었습니다.

**시스템 레벨**에서 분석을 통해 , 어떤 모듈이 bottleneck인지를 알 수 있습니다. 따라서, bottleneck인 모듈의 **커널**들을 분석하여 정확한 원인을 알 수 있습니다. 
## Profiling Using Nsight Compute


딥러닝 모델 최적화 과정에서, GPU 자원의 효율적인 활용은 성능을 극대화하는 데 중요한 요소입니다. Nvidia Nsight Compute는 GPU 연산의 세부적인 메트릭과 병목 현상을 분석할 수 있도록 지원하는 강력한 도구로, 특히 모델의 **커널 단위** 성능을 심층 분석하여 최적화에 필요한 인사이트를 제공합니다. 이를 통해 GPU 연산 효율성을 극대화하고, 모델의 추론 속도를 개선할 수 있습니다.

하지만 GPU 기반 모델에서는 종종 **커널 단위**의 연산 성능 저하와 비효율적인 자원 사용이 발생할 수 있습니다. Nsight Compute는 GPU 연산의 각 커널 성능을 심도 있게 분석함으로써, 성능 저하가 발생하는 지점과 그 원인을 구체적으로 파악할 수 있습니다. 이를 통해 모델 최적화 작업의 주요 목표를 설정할 수 있습니다.

이 과정에서 우선적으로 고려할 질문들은 다음과 같습니다:

1. **가장 비용이 많이 드는 커널은 무엇이며, 이를 최적화할 방법은 무엇인가?**  
   Nsight Compute는 각 커널의 실행 시간과 자원 사용량을 추적하여, 성능에 가장 큰 영향을 미치는 커널을 식별할 수 있습니다. 이를 통해 자원 소모가 많은 커널을 최적화할 기회를 제공합니다.

2. **메모리 병목을 줄이기 위해 어떤 최적화가 가능한가?**  
   GPU의 메모리 사용은 전체 연산 속도에 영향을 미칩니다. Nsight Compute는 메모리 대역폭 및 전송 효율성을 분석하여, 메모리 병목 현상을 완화할 수 있는 개선점을 제시합니다.

3. **각 GPU 연산의 발효율(occupancy)과 리소스 활용도를 어떻게 높일 수 있는가?**  
   Nsight Compute는 GPU 커널의 리소스 활용도를 시각화하여, 자원 활용률을 최적화할 수 있는 방안을 제공합니다.

이러한 분석을 통해, Nvidia Nsight Compute는 모델의 세부적인 GPU 연산 성능을 파악하고, 성능 최적화를 위한 구체적인 개선 방향을 제시할 수 있습니다. 이를 통해 최적화된 커널 성능과 리소스 활용이 이루어지면, 모델의 처리 속도와 효율성 역시 크게 향상될 수 있습니다.


### How to do profiling using Nsight Compute
```
ncu --set full --nvtx --nvtx-include "<range>" -o <file-name> --target-processes all  <app> <app-arg>
```  

라는 command를 통해서  분석을 할 수 있습니다.

위의 nsight systems와 겹치지 않은 부분만 설명을 하겠습니다.
- `--set` 은 어떤 metric을 collection할지 결정하는 것입니다. 여기에선, 모든 metric을 수집하도록 설정하였습니다. 
- `--nvtx` 은 nvtx로 정의한 range를 포함시키기 위한 option입니다.
- `--nvtx-include`  어떤 range를 포함시키는지를 나타냅니다. detail한 예시는 [링크](https://docs.nvidia.com/nsight-compute/Training/index.html#usage-examples)에서 확인할 수 있습니다. 
- `--target-processes` 는 해당 application이 호출하는 모든 function등을 trace 하는 option이라 볼 수 있습니다.

profiling이 성공적으로 완료되면 `ncu-rep` 이라는 확장자의 file을 얻게 됩니다 . 이 file을 open하게 되면 다음과 같은 화면이 나오게 됩니다.

![image](https://github.com/user-attachments/assets/7fbd8a4b-2b26-489d-8427-3c20d3ef1604)  


맨 아래에 뜨는 result는 Nvidia Nsight Compute Tool이 분석한 performance의 bottleneck 원인입니다.

`Page` 라는 드롭다운에서 `Summary` 대신에 `Details` 라는 option을 선택하면 다음과 같은 화면을 볼 수 있습니다. 

![image](https://github.com/user-attachments/assets/feb6f7f4-681b-4c5c-b4e3-09389646d33e)  

여기서 , 다양한 metric들을 분석할 수 있습니다. 자세한 metric에 대한 정보는 Documentation에서 확인할 수 있습니다.

### Nsight Compute Profiling Result
Nsight Compute의 Profiling 을 분석하기 위해서 `MemoryWorkload Analysis` 라는 metric을 분석하였습니다.왜냐하면, FlashAttention 논문에서 **Device Memory access**를 최소화 해서 성능을 최적화 한것에서 영감을 받았기 때문입니다 . 저는 이 Metric에서 **L2 cache**와 **Device Memory**간의 **data transfer size**를 module단위로 구하였습니다. 왜냐하면, **transfer size** 와 **memory access size**는 비례관계이기 때문입니다.
> $$transfer \; size  = bus \; width \cdot memory \; access $$ 라는 공식이 만족됩니다. 이론상의 bandwidth는 $$bandwidth  = bus \; width \cdot clock \; rate $$ 입니다. clockrate는 초당 수행가능한 instruction의 수를 의미합니다. 이론상의 bandwidth는 모든 instruction이 load,store 일 것입니다. 따라서, 특정한 size만큼의 memory를 주고 받았다면 이 size를 전달하기 위한 memory access는 bandwidth 공식을 적용하면 됩니다.  

이 때 transfer는 양방향으로 발생하기 때문에 read,write의 size를 전부 더해주어야 합니다. 그리고 , 각 module을 이루는 Kernel이 여러개이기 때문에 일일이 계산기로 구하였습니다. 왜냐하면, Nsight Compute GUI에서 csv같은 table format으로의 export를 지원하지 않기 때문입니다. (추후, csv로의 export option을 찾았습니다만 , 아직 실제로 적용해보지 못하였습니다. 적용하게 된다면 그 부분을 나중에 추가하도록 하겠습니다 .)
![image](https://github.com/user-attachments/assets/01b695dc-f622-4b8b-9d52-51495a66cd39)  

`Show As`라는 드롭다운에서 `Transfer size` 옵션을 선택하면 transfer size를 확인할 수 있습니다. 각 module의 transfer size와 duration(latency)를 plot해서 분석하였습니다.

- backbone
    - ![image](https://github.com/user-attachments/assets/f1fdfa61-cb43-4e4f-819f-8c43745131ec)
- encoder
    - ![image](https://github.com/user-attachments/assets/37401532-9e14-4713-a0dd-ed3d7062f644)
- decoder
    - ![image](https://github.com/user-attachments/assets/a8a0b414-6565-41ee-88c9-63ef9519faef)


이를 통해 Module의 latency는 transfer size와 비례한다는 insight를 얻었습니다. 


## Comprehensive analysis and optimization suggestions

딥러닝 모델의 성능 최적화를 위해서는 다양한 도구를 사용하여 성능을 분석하고, 이를 바탕으로 최적화 방향을 설정해야 합니다. Nsight Compute와 Nsight Systems는 각각 GPU의 세부적인 성능 분석과 시스템 레벨의 프로파일링을 제공하는 도구입니다. **두 도구를 통합**하여 사용함으로써, 모델의 성능 병목을 더 정확히 파악하고 효과적인 최적화 방안을 도출할 수 있습니다.

Nsight Compute는 GPU의 **세부적인 성능**을 분석하는 데 집중합니다. 주로 CUDA 커널의 실행 시간, 메모리 액세스, GPU 사용률 등의 지표를 제공합니다. 반면, Nsight Systems는 **전체 시스템 관점**에서 실행 흐름을 분석하며, CPU와 GPU의 상호작용, 데이터 전송 시간, 메모리 사용 패턴 등을 시각적으로 분석할 수 있게 해줍니다. 이 두 도구는 서로 보완적이며, 각 도구의 장점을 통합하면 더 깊이 있는 성능 분석을 할 수 있습니다.

두 도구를 통합했을 때 발생할 수 있는 주요 문제점은 각 도구에서 제공하는 정보의 해석이 서로 다를 수 있다는 점입니다. 예를 들어, Nsight Compute에서는 GPU 레벨의 성능 데이터를 제공하는 반면, Nsight Systems는 전체 시스템에서 발생하는 병목 현상에 대한 정보를 제공합니다. 이 두 가지를 결합하여 병목을 정확히 식별하지 않으면, 최적화가 비효율적으로 진행될 수 있습니다.

이러한 상황에서 우리가 던져야 할 주요 질문은 다음과 같습니다:

1. **Nsight Compute와 Nsight Systems의 결과를 어떻게 통합하여 분석할 수 있는가?**
   두 도구의 결과를 통합하려면, Nsight Compute에서 제공하는 GPU 성능 데이터를 기반으로, Nsight Systems에서 시스템 레벨의 성능 정보를 대조해야 합니다. 예를 들어, Nsight Compute에서 특정 커널이 병목을 일으킨다면, Nsight Systems에서 CPU와 GPU 간의 데이터 전송 시간이 길어지거나, I/O 작업에서 문제가 발생할 수 있음을 확인할 수 있습니다.

2. **각각의 도구에서 제공하는 성능 지표는 어떻게 해석하고, 병목을 정확히 파악할 수 있는가?**
   Nsight Compute에서 주로 제공하는 지표는 커널 실행 시간, 메모리 액세스 패턴, 텍스처 캐시 활용도 등이 있습니다. 이를 통해 GPU의 연산 성능과 메모리 사용에 대한 정보를 얻을 수 있습니다. Nsight Systems에서는 전체 시스템에서의 CPU와 GPU 활용도, 데이터 이동 시간 등을 분석할 수 있습니다. 이 데이터를 결합하면, GPU의 성능 문제뿐만 아니라 CPU와 GPU 간의 협력 문제도 식별할 수 있습니다.

3. **통합 분석을 통해 어떤 최적화 전략을 도출할 수 있는가?**
   통합된 분석을 통해 성능 병목을 정확히 식별한 후, 다양한 최적화 전략을 수립할 수 있습니다. 예를 들어, GPU의 성능 저하가 커널 실행 시간에 의한 것이라면, 커널을 최적화하거나 메모리 사용을 개선할 수 있습니다. 또한, CPU와 GPU 간의 데이터 전송 시간 문제라면, 데이터 전송 최적화나, 데이터 전처리 단계에서의 병목을 줄이는 방법을 고려할 수 있습니다.

결론적으로, Nsight Compute와 Nsight Systems의 결과를 통합하여 분석함으로써, 모델 성능 최적화를 위한 보다 정교한 인사이트를 얻을 수 있습니다. 이를 통해, 성능 병목을 정확히 파악하고, 이를 해결하기 위한 최적화 방안을 효과적으로 도출할 수 있습니다.


### Propose an optimized solution


모델 성능을 최적화하기 위한 핵심은 병목 현상을 정확히 식별하고, 이를 해결하기 위한 전략을 세우는 것입니다. Nsight Systems와 Nsight Compute를 활용한 분석을 통해, 성능 저하의 주요 원인을 찾아내고, 이를 기반으로 최적화 솔루션을 제안할 수 있습니다.

Nsight Systems를 사용하여 모델을 프로파일링한 결과, **가장 큰 latency**를 발생시키는 모듈을 식별할 수 있었습니다. 이 모듈은 **self-attention**과 **fpn** 부분으로, 성능 병목이 집중된 위치를 정확히 확인할 수 있었습니다. 이후, Nsight Compute를 통해 더 세부적으로 분석한 결과, latency와 transfer size가 비례한다는 사실을 발견했습니다. 즉, 전송되는 데이터의 크기가 커질수록, 해당 모듈의 latency가 증가하는 것으로 나타났습니다.

이러한 분석 결과를 바탕으로, transfer size를 줄이면 모듈의 latency를 감소시킬 수 있다는 결론을 도출할 수 있었습니다. transfer size를 최적화하는 방법에는 여러 가지가 있을 수 있습니다.그 중 하나는   **FlashAttention**과 같은 기술을 활용하여 **커널을 fusion**하는 방법이 있습니다. 이 방법은 여러 연산을 하나의 커널로 통합하여, 불필요한 데이터 전송을 줄이고, 메모리 접근 횟수를 최소화하는 방식입니다. 

따라서, transfer size를 줄이는 데 집중한 최적화는 다음과 같은 방향으로 진행할 수 있습니다. 첫 번째는 FlashAttention과 같은 **커널 Fusion** 기법을 사용하여 데이터 전송을 최소화하는 것입니다. 이러한 최적화 전략을 통해 모델의 latency를 크게 줄이고, 전반적인 성능을 개선할 수 있을 것입니다.



## References

[Nvidia nsight  systems requirements](https://docs.nvidia.com/nsight-systems/InstallationGuide/index.html#cuda-version)  
[Nvidia Nsight compute requirements](https://docs.nvidia.com/nsight-compute/ReleaseNotes/index.html#updates-in-2024-3-2)  
[GPU 메모리 대역폭 계산](https://chatgpt.com/share/672206fe-9514-800b-9fe2-812d99e71209)  
[Nvidia nsight systems references](https://docs.nvidia.com/nsight-systems/index.html#nsight-systems)  
[Nvidia nsight compute references](https://docs.nvidia.com/nsight-compute/index.html)
[Cuda Installation Guide for Linux](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/contents.html)  

