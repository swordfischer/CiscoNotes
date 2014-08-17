---
layout: post
title: Router/Switch Security
category: sheets
---
<div class="col-md-6">
 <h3>Configs</h3>
    R(config)# security min-length
    R(config)# exec-timeout <m> [s]
    R(config)# service password-encryption
    R(config)# username <name> secret ([0] password | encryption )
    R(config)# username <name> password ([0] password | encryption )
    R(config)# login block-for <m> attempts <t> within <s>
</div>
<div class="col-md-4">
 <div class="row">
  <h3>States/Show</h3>
 </div>
 <div class="row">
  <h3>Debug</h3>
 </div>
</div>
