---
layout: post
title: "[PHP] 게시판 페이징 컨트롤하기"
date: 2021-08-24
excerpt: "게시판 페이징기능 컨트롤 (주요공지사항 따로 구분하기)"
tags: [PHP, JavaScript, formValid, Codeigniter, Paging, 페이징]
comments: false
php: true
codeigniter: true
feature: /assets/img/php_logo.jpg
---


# 1. Controller
{% highlight php %}
 function notice()
{  
	$page = ($this->input->get("page",true)) ? $this->input->get("page",true) : 1;

	//주요공지 list
	unset($config);
	$config = array(   
        'orderby' => 'regist_date desc',
	    'select'=>'*,(SELECT COUNT(boardno) FROM mpl_board WHERE notice=1) AS cnt',
	    'where'=>array('notice'=>'1'), 
        'table'=>'mpl_board',
		  );
	$query = $this->common_model->_list($config);
	$count = $this->common_model->_total($config); 
	 
	$notice_list = null;
	$notice_list_fields = $this->db->list_fields($config['table']);
	foreach($query->result() as $row)
	{
		foreach ($query->list_fields() as $notice_list_field){
		   $notice_list_fld[$notice_list_field] = $row->$notice_list_field;
		}
		$notice_list[] = $notice_list_fld; 
		if($notice_list[0]['cnt']){
			$notice_count = $notice_list[0]['cnt'];
		} 
	}

	if(!$notice_count){
		$notice_count = 0;
	} 
	
	$static_listno = 10;								//한 페이지에 보여줄 게시글 수
	$temp_listno = $static_listno - $notice_count;		//한 페이지에 보여줄 게시글 수 - 주요 공지사항 개수 = $temp_listno. //  $temp_listno만큼 주요공지 밑으로 list찍기

	// 일반공지 list
	unset($config);
	$config = array(
            'page'=>$page,
            'listnum'=>$temp_listno, 
            'orderby' => 'regist_date desc', 
            'where'=>array(),
            'table'=>'mpl_board',
			  );
	$query = $this->common_model->_list($config);  
	$count = $this->common_model->_total($config); 
	  
	$list = null;
	$fields = $this->db->list_fields($config['table']);
	foreach($query->result() as $row)
	{
		foreach ($query->list_fields() as $field){
		   $fld[$field] = $row->$field;
		}
		$list[] = $fld; 
	}
   
	$listnum =$temp_listno;  //10
	$total_page = ceil($count / $listnum); 
	$list_no = $count - $listnum * ($page-1);
 		
	//	echo '<br>전체 게시글 수 : '.$count;
	//	echo '<br>전체 페이징 수 : '.$total_page; 
	//	echo '<br>한 페이지당 찍을 게시물 수  : '.$listnum;
	//	echo '<br>처음 글 번호 : '.$list_no; 
		
 
	$html ='pages/notice';
	$data = array(
			'_title' => null,
			'_contents' => 'view',
			'_skin' => 'single',
			'_language' => $this->language,
			'_menu' => null,
			'_view' => $html,
			'_param'=> $param,
			'_folder' =>$this->folder,
			'_list' => $list,   //글 전체 List
			'_notice_list' => $notice_list,  //주요공지 List
			'_list_no' => $list_no,
			'_page' => $page,
			'_total_page' =>$total_page,
			//'_paging'=> $this->pagination->create_links(),
		 
	);

	$data = array_merge($data); 
	$this->load->view('masterpage',$data); 
		
}
{% endhighlight %}

# 2. View
{% highlight php %}

<?php 

	$_prev_page = $_page - 1;
	$_next_page = $_page + 1;

	if(($_page - 1) <= 0){
		$_prev_page = 1;
	}

	if(($_page + 1) > $_total_page){
		$_next_page = $_total_page;
	} 
?>

<?php if($_total_page > 1):?>
<!-- 1. 현재페이지 = $_page-->
<div class="pagging">  

	<a href="/front/notice?page=1" class="move active first">처음</a><!-- 링크가 있을 경우 active를 주세요 --> 
	<a href="/front/notice?page=<?=$_prev_page?>" class="move active prev">이전</a> <!-- 링크가 있을 경우 active를 주세요 --> 
		  
	<?php for($i=1; $i<=$_total_page; $i++):?>
		<a href="/front/notice?page=<?=$i?>" class="num <?= ($_page == $i)? 'active' : '' ?>"><?=$i?></a>  
	<?php endfor;?>

       <a href="/front/notice?page=<?=$_next_page?>" class="move active next">다음</a> <!-- 링크가 있을 경우 active를 주세요 --> 
	<a href="/front/notice?page=<?=$_total_page?>" class="move active last">마지막</a> <!-- 링크가 있을 경우 active를 주세요 --> 
 
</div> 
    
<?php endif;?>
{% endhighlight %}
