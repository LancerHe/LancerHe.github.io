---
title: 学习CI框架之MVC模型开发小记(二)
author: 谇雨
excerpt: |
  <p style="text-indent: 25px;">今天学习了CI的Model模型，总的来说Model完全就是用来给数据库打交道用了。
  在models文件架建立blog_model.php用于给文章的数据增加，更新，查询的Model.</p>
  <?php
  <coolcode lang="php" linenum="off">
  class Blog_model extends Model {
  	
  	public $post_title;
  	public $post_content;
  	public $post_date;
  	......
  </coolcode>
layout: post
permalink: /ci-study-mvc-summary-2.html
views:
  - 769
categories:
  - CodeIgniter
tags:
  - CI
  - model
  - php
  - 框架
---
<p style="text-indent: 25px;">
  今天学习了CI的Model模型，总的来说Model完全就是用来给数据库打交道用了。<br /> 在models文件架建立blog_model.php用于给文章的数据增加，更新，查询的Model。
</p>

<pre class="lang:php decode:true " >&lt;?php
class Blog_model extends Model {
	
	public $post_title;
	public $post_content;
	public $post_date;
	
	function __contrust(){
		parent::Model();
	}
	
	function insert_art(){
		$this-&gt;post_content = $this-&gt;input-&gt;post('content');
		$this-&gt;post_title   = $this-&gt;input-&gt;post('title');
		$this-&gt;post_date    = date('Y-m-d H:m:s');
		$this-&gt;db-&gt;insert('posts',$this);
	}
	
	function update_art($id){
		$this-&gt;post_content = $this-&gt;input-&gt;post('content');
		$this-&gt;post_title   = $this-&gt;input-&gt;post('title');
		$this-&gt;post_date    = date('Y-m-d H:m:s');
		$this-&gt;db-&gt;update('posts',$this,array('id'=&gt;$id));
	}
	
	function get_ten_art() {
		$sql   = "select post_content,post_title,post_date from wp_posts limit 3";
		$query = $this-&gt;db-&gt;query($sql);
		return $query-&gt;result_array();
	}
}
?&gt;</pre>

<!--more-->

  
在控制器页面：

<pre class="lang:default decode:true " >&lt;?php
class Blog extends Controller{
	function __construct(){
		parent::Controller();
		//构造时，加载模型
		$this-&gt;load-&gt;model('Blog_model','',TRUE);
	}

	function index(){
		$data['title'] = 'my web';
		$data['art']   = $this-&gt;Blog_model-&gt;get_ten_art();

		$this-&gt;load-&gt;view('blog_view',$data);
	}
	
	function insert(){
		$this-&gt;Blog_model-&gt;insert_art();
	}
	
	function update($id){
		$this-&gt;Blog_model-&gt;update_art($id);
	}
}
?&gt;</pre>