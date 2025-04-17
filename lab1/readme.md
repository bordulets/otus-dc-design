### Домашнее задание №1
# IP-план

### схема сети

![scheme.png](scheme.png)


### план адресации

|Devicce|Interface|IP Address/Mask|Link to|
|---|---|---|---|
Spine1|Lo1|10.10.1.0/32|-
Spine1|Lo2|10.11.1.0/32|-
Spine1|Eth1/1|10.20.1.0/31|Leaf1
Spine1|Eth1/2|10.20.1.2/31|Leaf2
Spine1|Eth1/3|10.20.1.4/31|Leaf3
 | | | 
Spine2|Lo1|10.10.2.0/32|-
Spine2|Lo2|10.11.2.0/32|-
Spine2|Eth1/1|10.20.2.0/31|Leaf1
Spine2|Eth1/2|10.20.2.2/31|Leaf2
Spine2|Eth1/3|10.20.2.4/31|Leaf3
 | | | 
Leaf1|Lo1|10.10.0.1/32|-
Leaf1|Lo2|10.11.0.2/32|-
Leaf1|Eth1/1|10.20.1.1/31|Spine1
Leaf1|Eth1/2|10.20.2.1/31|Spine2
 | | | 
Leaf2|Lo1|10.10.0.2/32|-
Leaf2|Lo2|10.11.0.2/32|-
Leaf2|Eth1/1|10.20.1.3/31|Spine1
Leaf2|Eth1/2|10.20.2.3/31|Spine2
 | | | 
Leaf3|Lo1|10.10.0.3/32|-
Leaf3|Lo2|10.11.0.3/32|-
Leaf3|Eth1/1|10.20.1.5/31|Spine1
Leaf3|Eth1/2|10.20.2.5/31|Spine2
