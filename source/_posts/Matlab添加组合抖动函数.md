---
title: MTALAB 中 commsrc.combinedjitter 构造组合抖动生成器对象
date: 2024-07-08 19:17:17
tags:
    - 学习笔记
    - MATLAB
    - 研究生
---
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

### 3.2 生成一个PRBS7信号并添加RJ和DJ
可运行的代码如下：
```MATLAB
% 利用模式生成器对象生成一个二进制非归零(NRZ)信号。查看NRZ信号与不抖动应用到信号。
% 初始化系统参数
Fs = 10000; % Sample rate 采样率
Rs = 50; % Symbol rate 符号率
sps = Fs / Rs; % Number of samples per symbol 每个符号的样本数
Trise = 1 / (5 * Rs); % Rise time of the NRZ signal NRZ信号的上升时间
Tfall = 1 / (5 * Rs); % Fall time of the NRZ signal NRZ信号的下降时间
frameLen = 100; % Number of symbols in a frame 一帧中的符号数

% 创建无抖动的PRBS信号源对象
src_no_jitter = commsrc.pattern('SamplingFrequency', Fs, ...
    'SamplesPerSymbol', sps, 'RiseTime', Trise, 'FallTime', Tfall, ...
    'DataPattern', 'PRBS7'); % 使用PRBS7作为数据模式

% 生成无抖动的PRBS信号
message_no_jitter = generate(src_no_jitter, frameLen);

% 创建一个包含随机抖动和周期抖动的抖动对象
jitter = commsrc.combinedjitter('RandomJitter', 'on', 'RandomStd', 2e-4, ...
                                'PeriodicJitter', 'on', 'PeriodicAmplitude', 1e-4, ...
                                'PeriodicFrequency', 1000);

% 创建一个带抖动的PRBS信号源对象
src_with_jitter = commsrc.pattern('SamplingFrequency', Fs, ...
    'SamplesPerSymbol', sps, 'RiseTime', Trise, 'FallTime', Tfall, ...
    'DataPattern', 'PRBS7', 'Jitter', jitter); % 使用PRBS7作为数据模式

% 生成带抖动的PRBS信号
message_with_jitter = generate(src_with_jitter, frameLen);
message_with_jitter = message_with_jitter(1:20000);
% 创建时间向量
t = (0:length(message_no_jitter)-1) / Fs;

% 绘制时域图
figure;
plot(t, message_no_jitter, 'b'); hold on;
plot(t, message_with_jitter, 'r');
legend('无抖动', '带抖动');
title('PRBS信号时域图对比');
xlabel('时间 (秒)');
ylabel('幅度');
grid on;
hold off;

%求TIE图-------------
signal_out = [t',message_no_jitter];
signal_in = [t',message_with_jitter];

%求上升沿下降沿与中线的交点
[edge_interp_in,piont_index_in]=func_sig_edge(signal_in,0.5,5);
[edge_interp_out,piont_index_out]=func_sig_edge(signal_out,0.5,5);

TIE_length = min(length(edge_interp_in), length(edge_interp_out));
%求交点的时间差，即TIE
TIE = zeros(TIE_length, 0);
for i = 1:TIE_length
    TIE(i) = edge_interp_out(i) - edge_interp_in(i);
end
%TIE直方图分布
figure(5);
histogram(TIE, 200);
%TIE趋势图
figure(4);
t_TIE =  1 : 1 : length(TIE);
plot(t_TIE,TIE);

```

目前发现添加抖动是通过generate函数进行添加，该generate方法在pattern201行下。调试进入该函数如下：
```MATLAB
        function y = generate(this, N)
%GENERATE  Generate a modulated pattern
%   Y = GENERATE(H, N) generates an N symbol modulated pattern based on the
%   pattern generator object H.
%
%   See also COMMSRC.PATTERN, COMMSRC.PATTERN/STD181TOIDEAL,
%   COMMSRC.PATTERN/IDEALTOSTD181, COMMSRC.PATTERN/RESET,
%   COMMSRC.PATTERN/COMPUTEDCD, COMMSRC.PATTERN/DISP. 

            % Check input arguments
            narginchk(2, 2);    % 检查输入参数个数为2-2
            sigdatatypes.checkFinitePosIntScalar('GENERATE', 'N', N);  % 检查N是否为有限正整数标量

            % Get data bits
            if strncmp(this.DataPattern, 'U', 1)    % 判断信号数据是不是User Defined
                % Get bits from user data
                
                % Get data vector and counter
                userDataPattern = this.UserDataPattern(:);
                cnt = this.UserDataCnt;
                len = length(userDataPattern);
                
                if len < N
                    % Length of user data is less than requested length
                    data = repmat(userDataPattern, ceil((N+cnt)/len), 1);
                    data = data(cnt:cnt+N-1);
                else
                    % Length of user data is greater than or equal to the
                    % requested length 
                    if (cnt+N) > len
                        data = [userDataPattern(cnt:end); ...
                            userDataPattern(1:N+cnt-len-1)];
                    else
                        data = userDataPattern(cnt:cnt+N-1);
                    end
                end
                
                % Update counter
                this.UserDataCnt = mod(cnt + N - 1, len) + 1;
            else
                % Get bits from PRBS generator
                hDataGen = this.DataPatternGen;
                data = hDataGen(N);
            end
            
            if this.SamplingFrequency ~= this.Jitter.SamplingFrequency
              this.Jitter.SamplingFrequency = this.SamplingFrequency;
            end

            % Generate jitter samples
            jitter = generate(this.Jitter, N);
            
            % Generate the jitter injected pulse.  Note that the pulse generator
            % requires jitter values to be in samples.
            y = generate(this.PulseGenerator, data, ...
                jitter*this.SamplingFrequency);% ***产生抖动注入脉冲
        end
```
上面最后一行generate函数进入如下，这里是如何将产生的抖动成分添加到PRBS信号的核心成分。
```MATLAB
function y = generate(this, data, varargin)
%GENERATE   Generate NRZ modulated signal
%   GENERATE(H, DATA) modulates input data bits, DATA, using NRZ modulated
%   signaling.  DATA must be a column vector with element values 0 or 1.  The
%   NRZ pulse properties are defined by the NRZ pulse generator object H.  
%
%   GENERATE(H, DATA, JITTER) generates NRZ modulated signals as in the previous
%   case but also injects jitter.  JITTER is a column vector of real jitter
%   values.  Jitter values are normalized with sampling frequency.  The length
%   of DATA and JITTER vectors must be equal.  The difference of consecutive
%   jitter values must be less than the symbol duration. 
%
%   EXAMPLES:
%
%     % Create an NRZ pulse generator object
%     h = commsrc.nrz;
%     % Generate binary data
%     data = rand(20, 1) > 0.5;
%     % Generate NRZ modulated signal
%     y = generate(h, data);
%     plot(y)
%
%     % Generate random jitter
%     jitter = randn(20, 1)/50;
%     % Generate NRZ modulated signal and inject jitter
%     y = generate(h, data, jitter);
%     hold on; plot(y, 'r'); hold off;
%
%   See also COMMSRC.NRZ, COMMSRC.NRZ/DISP, COMMSRC.NRZ/RESET.

% Copyright 2008-2009 The MathWorks, Inc.

% Parse and validate input arguments
[jitter dataLen] = parseGenerateArgs(this, data, varargin{:});% 这里进入函数，检查一些问题，最终得到抖动成分数据和jitter原输入信号长度dataLen

% Gather needed information to generate the output
sqrtEps = sqrt(eps);
riseRate = this.RiseRate;
fallRate = this.FallRate;
numRiseSamps = this.NumRiseSamps;
numFallSamps = this.NumFallSamps;
high = this.HighLevel;
low = this.LowLevel;

% Append the last transmitted data symbol index to DATA
data = [this.LastData; data];

% Obtain symbols from symbol numbers
symbols = this.OutputLevels(data+1);% 这里把01信号变成了-1和1信号

% Get jittered clock ********核心部分，将抖动成分掺入时钟*********
[clk nClk rClk] = getJitteredClock(this, dataLen, jitter);

% Determine difference between adjacent data levels.  We will use this vector to
% decide if we have a falling transition, a rising transition, or no transition.
change = diff(symbols);% 确定相邻数据电平之间的差异。我们将使用这个向量来判断是下降过渡、上升过渡还是没有过渡。

% Generate output. 
y = zeros(nClk(end)-1, 1);
for p=1:length(clk)-1
    if change(p) < -sqrtEps
        % This is a high-to-low transition
        
        % Calculate the end time of the integer part of the fall time
        t1 = floor(clk(p)+numFallSamps);
        
        y(nClk(p)) = high + fallRate*rClk(p);
        for q=nClk(p)+1:t1
            y(q) = y(q-1) + fallRate;
        end
        y(t1+1:nClk(p+1)-1) = low;
    elseif change(p) > sqrtEps
        % This is a low-to-high transition

        % Calculate the end time of the integer part of the rise time
        t1 = floor(clk(p)+numRiseSamps);
        
        y(nClk(p)) = low + riseRate*rClk(p);
        for q=nClk(p)+1:t1
            y(q) = y(q-1) + riseRate;
        end
        y(t1+1:nClk(p+1)-1) = high;
    else
        % No transition
        y(nClk(p):nClk(p+1)-1) = symbols(p+1);
    end
end

% Store state
this.LastData = data(p+1);
this.LastJitter = jitter(end);
```