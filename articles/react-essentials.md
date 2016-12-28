一個應用程式包含UI和data，事件會影響data，這些事件包括使用者觸發的事件和伺服端觸發的事件，而UI會隨著model變化而改變。

UI在web技術上，主要和DOM相關。為了使UI和data能夠同時變化，而不是讓開發者手動改UI，有許多同步的方式。Ember.js, Backbone等是使用雙向綁定，而Angular是用dirty checking。

React使用virtual DOM。virtual DOM是當前DOM的一個副本，存在記憶體中。一旦data發生變化，會先在記憶體生成(render)新的virtual DOM，在比較完新舊的virtual DOM後，再針對有差異的部份，實際更新DOM。