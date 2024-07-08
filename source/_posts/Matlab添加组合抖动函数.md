# MTALAB 中 commsrc.combinedjitter 构造组合抖动生成器对象
## 1. 两种函数
```MATLAB
combJitt = commsrc.combinedjitter
combJitt = commsrc.combinedjitter(Name,Value)
```
## 2. 定义
combJitt = commsrc.combinedjitter构造一个默认的组合抖动生成器对象combJitt，所有的抖动组件都被禁用。

使用该对象可生成抖动样本，其中包括随机、周期性和狄拉克分量的任意组合。

---
combJitt = commsrc.combinedjitter(Name,Value)创建一个组合抖动生成器对象，并将指定的属性Name设置为指定的Value。可以任意顺序指定附加的名称-值对参数，如(Name1,Value1，…，NameN,ValueN)。

## 3. 案例
### 3.1 生成500个由随机和周期抖动组成的抖动样本
创建一个commsrc.combinedjitter对象，配置为应用随机和周期性抖动组件的组合。使用名称-值对来启用RandomJitter和PeriodicJitter，并分配抖动设置。设置随机抖动的标准差为2e-4秒，周期抖动的幅度为5e-4秒，周期抖动的频率为2hz。
```MATLAB
% step1: 创建一个commsrc.combinedjitter对象，对抖动成分进行设置
numSamples = 500;
combJitt = commsrc.combinedjitter(...
    'RandomJitter','on', ...
    'RandomStd',2e-4, ...
    'PeriodicJitter','on', ...
    'PeriodicAmplitude',5e-4, ...
    'PeriodicFrequencyHz',200);  
% step2: 使用生成方法generate创建组合抖动样本。
y = generate(combJitt,numSamples);
x = [0:numSamples-1];
% step3: 绘制抖动样本
plot(x/combJitt.SamplingFrequency,y);
xlabel('Time (seconds)');
ylabel('Jitter (seconds)');
```
