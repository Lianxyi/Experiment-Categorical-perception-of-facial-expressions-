# 面部表情的类比知觉效应实验教程（jspsych代码示例）

## 实验简介

类别知觉(Categorical Perception, CP)是指事物在人脑中是按照类别表征的，而不是按照连续的物理信号表征的，且类别之间存在鲜明的类别界线。刺激从一种类别到另一种类别的知觉转折点叫做类别界线，跨类别界线的一对刺激叫做类间(between-category)刺激，如快乐表情和悲伤表情，属于一个类别的一对刺激叫做类内(within-category)刺激，如不同程度的悲伤表情。与类内表情相比，类间表情的辨别正确率更高，反应时更短，这是类别知觉最重要的特征。本实验采用识别任务探究面部表情的类别知觉效应。

## 实验材料

 面部表情类别知觉研究的实验材料是物理刺激均匀变化的表情连续体，连续体两端是两种不同表情的原型，如快乐—悲伤连续体。将连续体分为 n 个等距的刺激材料。本实验中采用的实验材料是将快乐—悲伤连续体分为6张等距变化的面部表情，如图1。

<img src="https://s2.loli.net/2024/07/06/xvZtEqVRJTw5eBi.png" alt="1.png" style="zoom:80%;" />

​                                                                                                                图1

注：实验材料需要单独下载stimuli.zip文件，并解压将每张刺激图片都放在与代码文件相同的文件夹中

## 实验程序

实验中被试需要判断图片中的表情属于高兴还是悲伤，并进行相应的按键。实验中共6张图片，每张图片随机呈现5次，共30个trails。记录被试的反应时和选择情况。

## 实验代码

实验程序使用 jsPsych 库开发

### 实验布局代码（style.css文件）

1. 确保页面和容器占满整个浏览器窗口

   ```
   body, html {
       height: 100%;
       margin: 0;
       display: flex;
       justify-content: center;
       align-items: center;
       overflow: hidden;
   }
   ```

2. 定义实验容器的布局和样式

   ```
   .experiment-container {
       width: 100%;
       max-width: 1200px;
       text-align: center;
       padding: 20px; 
   }
   ```

3.  将实验内容居中对齐

   ```
   .jspsych-content {
       display: flex;
       justify-content: center;
       align-items: center; 
       text-align: center; 
       white-space: nowrap; 
   }
   ```

4.  将实验显示的元素居中

   ```
   .jspsych-display-element {
       margin: 0 auto;
   }
   ```

5. 图片最大宽度和高度

   ```
   img {
       max-width: 100%;
       max-height: 100%;
   }
   ```

### 实验程序代码（experiment.html文件）

1. 代码声明部分，引用了外部CSS文件用于定义网页的样式，引用 jsPsych 库以及jsPsych 的两个插件，分别用于 HTML 键盘响应和图像键盘响应。

   ```
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Emotion Recognition Experiment</title>
       <link rel="stylesheet" href="style.css"> 
       <script src="https://cdn.jsdelivr.net/npm/jspsych@6.3.1/jspsych.js"></script>
       <script src="https://cdn.jsdelivr.net/npm/jspsych@6.3.1/plugins/jspsych-html-keyboard-response.js"></script>
       <script src="https://cdn.jsdelivr.net/npm/jspsych@6.3.1/plugins/jspsych-image-keyboard-response.js"></script>
       <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/jspsych@6.3.1/css/jspsych.css">
   </head>
   <body>
       <div class="experiment-container" id="jspsych-target">
           <script>
   ```

2. 创建一个时间线数组

   ```
    var timeline = [];
   ```

3. 设置实验欢迎界面

   ```
   var welcome = {
                   type: "html-keyboard-response",
                   stimulus: "<p>欢迎参加实验！</p><p>按任意键开始。</p>",
                   post_trial_gap: 1000 
               };
               timeline.push(welcome);
   ```

4. 设置实验指导语界面

   ```
   var instructions = {
                   type: "html-keyboard-response",
                   stimulus: "<p>如果图片中的表情是高兴的，请按 'F' 键。</p>" +
                             "<p>如果图片中的表情是悲伤的，请按 'J' 键。</p>" +
                             "<p>按任意键继续。</p>",
                   post_trial_gap: 1000 
               };
               timeline.push(instructions);
   ```

5. 导入刺激图片

   ```
     var test_stimuli = [
                   { stimulus: 'ha_sa1.png' },
                   { stimulus: 'ha_sa2.png' },
                   { stimulus: 'ha_sa3.png' },
                   { stimulus: 'ha_sa4.png' },
                   { stimulus: 'ha_sa5.png' },
                   { stimulus: 'ha_sa6.png' }
               ];
   ```

6. 生成试次数组，每个图片随机出现5次

   ```
    var trials = [];
               for (var i = 0; i < 5; i++) {
                   jsPsych.randomization.shuffle(test_stimuli).forEach(function(item) {
                       trials.push(item);
                   });
               }
   ```

7. 创建图片试次

   ```
   var test = {
                   type: "image-keyboard-response",
                   stimulus: jsPsych.timelineVariable('stimulus'),
                   choices: ['f', 'j'],
                   data: {
                       image: jsPsych.timelineVariable('stimulus')
                   },
                   on_finish: function(data){
                       data.key_press = jsPsych.pluginAPI.convertKeyCodeToKeyCharacter(data.key_press);
                   }
               };
   ```

8. 添加试次到时间线

   ```
   var test_procedure = {
                   timeline: [test],
                   timeline_variables: trials
               };
               timeline.push(test_procedure);
   ```

9. 设置结束界面

   ```
     var debrief = {
                   type: "html-keyboard-response",
                   stimulus: function() {
                       return "<p>实验结束，感谢参与！</p>" +
                              "<p>按任意键退出。</p>";
                   },
                   on_finish: function() {
                       var csv = jsPsych.data.get().csv();
                       var blob = new Blob([csv], { type: 'text/csv' });
                       var url = URL.createObjectURL(blob);
                       var a = document.createElement('a');
                       a.href = url;
                       a.download = 'experiment_data.csv';
                       document.body.appendChild(a);
                       a.click();
                       document.body.removeChild(a);
                   }
               };
               timeline.push(debrief);
   ```

10. 设置实验开始

```
jsPsych.init({
                timeline: timeline,
                display_element: 'jspsych-target'
            });
        </script>
    </div>
</body>
</html>
```

## 数据结果和分析

数据结果将存储于csv格式的文件中。分别计算被试选择高兴和难过表情的比率并绘制结果图（如图2示例）。

<img src="https://s2.loli.net/2024/07/06/EJb2BaNIzZGTYjP.png" alt="2.png" style="zoom: 50%;" />

​                                                                                                                        图2

