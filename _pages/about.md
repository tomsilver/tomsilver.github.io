---
layout: about
title: about
permalink: /
subtitle: PhD Candidate at <a href="https://www.csail.mit.edu/">MIT CSAIL</a>

profile:
  align: right
  image: tomsilver3-high-res.jpg
  image_circular: false # crops the image to make it circular
  more_info:

news: false # includes a list of news items
selected_papers: false # includes a list of papers marked as "selected={true}"
social: true # includes social icons at the bottom of the page

---
I am a final year PhD student at MIT EECS. My research is motivated by the prospect of <b>broadly-competent</b>, <b>helpful</b>, and <b>intelligent</b> robots that can respond to very high-level commands like "make me a heart-healthy dinner"; learn new skills like "grind fresh pepper"; and learn new concepts like "wilted spinach." Such robots will be especially transformative for people who cannot otherwise remain independent in their homes. Most of my work is at the intersection of automated planning and machine learning: <b>learning to plan</b> and <b>planning to learn</b> while making <b>efficient</b> use of limited data and time. I often use techniques from <a href="https://arxiv.org/abs/2010.01083">task and motion planning</a>, <a href="https://arxiv.org/abs/1904.06317">program synthesis</a>, and <a href="https://arxiv.org/abs/2206.10680">neuro-symbolic machine learning</a>.

At MIT, I am advised by Leslie Kaelbling and Joshua Tenenbaum and am a member of the <a href="https://lis.csail.mit.edu/">Learning and Intelligent Systems</a> group and the <a href="https://cocosci.mit.edu/">Computational Cognitive Science</a> group. Previously I was a researcher at Vicarious AI. I received my B.A. from Harvard with highest honors in computer science and mathematics in 2016. I have also interned at Google Research (Brain Robotics) and currently split my time between MIT and the <a href="https://theaiinstitute.com/">Boston Dynamics AI Institute</a>. I am grateful for support from an NSF Graduate Research Fellowship and an MIT Presidential Fellowship.

<h3 id="research">research areas</h3>
<p><br></p>

<div class="container">

  <!-- Learning abstractions for hierarchical planning -->
  <div class="row">
    <div class="col-5">
      <p><img src="assets/img/behavior-blog-post-image12.gif" style="width: 300px; height: 200px;"></p>
    </div>
    <div class="col-7">
      <h5 id="learning-abstractions-for-planning">Learning abstractions for hierarchical planning</h5>
      <p>Abstractions allow robots to first focus on the high-level aspects of a task before getting bogged down in details. We would like a robot to automatically learn abstractions&mdash;<a href="https://arxiv.org/abs/2203.09634" target="_blank" rel="noopener noreferrer">state abstractions (predicates)</a> and <a href="https://arxiv.org/abs/2206.10680" target="_blank" rel="noopener noreferrer">action abstractions (skills)</a>&mdash;that are specialized for planning in its domain. We are especially interested in abstractions for <a href="https://arxiv.org/abs/2010.01083">task and motion planning</a>. (Image credit: <a href="https://nishanthjkumar.com/">Nishanth Kumar</a>)</p>
    </div>
  </div>

  <!-- Program synthesis for planning -->
  <div class="row">
    <div class="col-5">
     <p><video controls playsinline autoplay muted loop width="300" style="margin-top:10px; margin-right:50px;"><source src="assets/video/blocksworld-program-synthesis.mp4" type="video/mp4" /></video></p>
    </div>
    <div class="col-7">
      <h5 id="program-synthesis-for-planning">Program synthesis for planning</h5>
      <p>We want robots to be like self-supervised software engineers, writing their own code and growing libraries that can be used to solve increasingly difficult decision-making problems. We use <a href="https://arxiv.org/abs/2305.11014">LLMs</a>, <a href="https://arxiv.org/abs/1904.06317">Bayesian program learning</a>, <a href="https://arxiv.org/abs/2005.02259">inductive logic programming</a>, <a href="https://arxiv.org/abs/2109.11082">SAT solvers</a>, and <a href="https://arxiv.org/abs/2204.10420">heuristic search</a> to synthesize programs.</p>
    </div>
  </div>

  <!-- Learning to accelerate planning -->
  <div class="row">
    <div class="col-5">
     <p><video controls playsinline autoplay muted loop width="300" style="float: left; margin-top:5px; margin-right:50px;"><source src="assets/video/ploi-compressed.mp4" type="video/mp4" /></video></p>
    </div>
    <div class="col-7">
      <h5 id="learning-to-accelerate-planning">Learning to accelerate planning</h5>
      <p>Even with good abstractions, online planning can be slow, especially in high-dimensional environments with many objects. Robots should learn to plan better and faster over time. We can automatically accelerate planning by learning <a href="https://arxiv.org/abs/2009.05613">object-centric task abstractions</a>, learning to <a href="https://arxiv.org/abs/2007.13202">self-impose constraints</a>, or learning <a href="https://arxiv.org/abs/2109.14830">heuristics</a>.</p>
    </div>
  </div>

  <!-- Planning to learn -->
  <div class="row">
    <div class="col-5">
     <p><video controls playsinline autoplay muted loop width="300" style="float: left; margin-top:5px; margin-right:50px;"><source src="assets/video/spot-sweeping-compressed.mp4" type="video/mp4" /></video></p>
    </div>
    <div class="col-7">
      <h5 id="planning-to-learn">Planning to learn</h5>
      <p>Robots should plan to practice to get better at planning. They should rapidly learn to specialize to the objects, goals, preferences, and constraints that are unique to their deployment. We can plan to learn <a href="https://arxiv.org/abs/2402.15025">samplers</a>, <a href="https://arxiv.org/abs/2303.04912">predicates</a>, and <a href="https://arxiv.org/abs/2001.08299">operators</a> for bilevel planning. Our ultimate goal is to create a virtuous cycle of learning and planning.</p>
    </div>
  </div>

</div>
