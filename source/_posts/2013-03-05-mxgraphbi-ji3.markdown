---
layout: post
title: "mxgraph笔记3【toolbar】"
date: 2013-03-05 13:37
comments: true
categories: [js,mxgraph]
---

用给定的dom元素创建一个toolbar，这个toolbar可能包含icons，buttons和combo boxes
<!--more-->
例子
	<div id="sidebar">
		<ul style="display: block;">
			<li class="submenu">
				<a href="#"> <i class="icon icon-white icon-inbox"></i>
					<span>基本</span>
					<span class="label">3</span>
				</a>
				<ul id="toolbar-basic">
				</ul>
			</li>
			<li class="submenu">
				<a href="#"> <i class="icon icon-white icon-th-list"></i>
					<span>图形</span>
					<span class="label">3</span>
				</a>
				<ul id="toolbar-shpae">
				</ul>
			</li>
		</ul>
	</div>
	...
	// Creates the div for the toolbar basic
	var containerBasic = document.getElementById('toolbar-basic');

	var toolbarBasic = new mxToolbar(containerBasic);
	toolbarBasic.enabled = false;

	new mxDivResizer(containerBasic);

	// Creates the div for the toolbar basic
	var containerShape = document.getElementById('toolbar-shape');

	var toolbarShape = new mxToolbar(containerShape);
	toolbarShape.enabled = false;

	new mxDivResizer(containerShape);


