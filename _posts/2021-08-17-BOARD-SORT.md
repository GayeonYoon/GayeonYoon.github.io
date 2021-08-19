---
layout: post
title: "[PHP] 그누보드5 BOARD 게시글 순서 변경하기"
date: 2021-08-17
excerpt: "그누보드5 BOARD 게시글 순서 변경하기"
tags: [PHP, JavaScript, Sort, Sorting, 그누보드5]
comments: false
php: true
codeigniter: true
feature: /assets/img/php_logo.jpg
---


# 1.  www/bbs/list_update_num.php

{% highlight php %}
<?php
 //include_once '../../../lib/common.lib.php';
include_once('./_common.php');
include_once(G5_LIB_PATH.'/naver_syndi.lib.php');
include_once(G5_CAPTCHA_PATH.'/captcha.lib.php');
 
//====================================
		//검색  
		$dbjob  = $_POST['dbjob'];
		$sort_no  =  $_POST['sort_no'];
		$seq  =  $_POST['seq']; 
		
		$bo_table = $_POST['bo_table']; 
  
		$p[] = 'cate='.$cate;
		$p[] = 'keyname='.$keyname;
		$p[] = 'keyword='.urlencode($keyword);
		$param = '&'.implode('&',$p);

			
		//상품 순서정렬 조건절
		$_where = array();
		if($cate) $_where[] = " cate='$cate' ";
		if($keyname && $keyword) $_where[] = $keyname." LIKE '%".$keyword."%'";
		$where = (sizeof($_where)>0)?' AND '.implode(' AND ',$_where):'';
 
		unset($config);	
		$config['dbjob'] = $dbjob;
		$config['where'] = array('wr_id'=>$seq);
		$config['table'] = 'g5_write_'.$bo_table;
 
 
		switch($dbjob)
		{
			case('down'):
			$msg = '수정되었습니다.'; 
			$row =sql_fetch('SELECT wr_id,wr_num,count(*) as cnt FROM '.$config['table'].' WHERE wr_num > '.$sort_no.' ORDER BY wr_num desc LIMIT 1'); 
	  
			if($row['cnt'] > 0){
				$sql = "update ".$config['table']." set wr_num='{$row['wr_num']}' where wr_id = '{$seq}' ";
				sql_query($sql);
				$sql2 = "update ".$config['table']." set wr_num='{$sort_no}' where wr_id = '{$row['wr_id']}' ";
				sql_query($sql2);
 
				echo '<script>alert("수정되었습니다.");</script>';
			}

			break;

			case('up'):
	 
			$msg = '수정되었습니다.'; 
			$row =sql_fetch('SELECT wr_id,wr_num  FROM '.$config['table'].' WHERE wr_num < '.$sort_no.'  ORDER BY wr_num desc LIMIT 1'); 
			$row2=sql_fetch('SELECT count(*) as cnt  FROM '.$config['table'].' WHERE wr_num < '.$sort_no.'  ORDER BY wr_num desc LIMIT 1'); 
			
			 if($row2['cnt'] > 0){
				$sql = "update ".$config['table']." set wr_num='{$row['wr_num']}' where wr_id = '{$seq}' ";
				sql_query($sql);
				$sql2 = "update ".$config['table']." set wr_num='{$sort_no}' where wr_id = '{$row['wr_id']}' ";
				sql_query($sql2);

				echo '<script>alert("수정되었습니다.");</script>';
		 	}

			break;
		}
	
	echo '<script> location.href="/bbs/board.php?bo_table='.$bo_table.'";</script>';
?> 

{% endhighlight %}

# 2. www/skin/board/list.skin.php
{% highlight php %}
<form name="fboardlist" id="fboardlist" action="./board_list_update.php" onsubmit="return fboardlist_submit(this);" method="post">
	<input type="hidden" name="dbjob" value="">
	<input type="hidden" name="seq" value="">
	<input type="hidden" name="sort_no" value="">
  
  
  <!--각 게시물 리스트 위에 추가 -->
	<div style="float:right;">
		<a href="javascript:sortGoods('up','<?=$result[$k][$i]['wr_id']?>','<?=$result[$k][$i]['wr_num']?>')" class="arrow-up"></a>
		<a href="javascript:sortGoods('down','<?=$result[$k][$i]['wr_id']?>','<?=$result[$k][$i]['wr_num']?>')" class="arrow-down"></a>
	</div>
</form>


<!-- CSS 추가-->

<style>
a.plus{width:25px;height:25px;background:url('/images/icon01.png') no-repeat -86px center;}
a.minus{width:25px;height:25px;background:url('/images/icon01.png') no-repeat -58px center;}
    
a.arrow-up{width:25px;height:25px;background:url('/img/icon01.png') no-repeat 0 center;overflow:hidden;display:inline-block}
a.arrow-down{width:25px;height:25px;background:url('/img/icon01.png') no-repeat -28px center;overflow:hidden;display:inline-block}
a.arrow-left{width:25px;height:25px;background:url('/img/icon01.png') no-repeat -144px center;overflow:hidden;display:inline-block}
a.arrow-right{width:25px;height:25px;background:url('/img/icon01.png') no-repeat -116px center;overflow:hidden;display:inline-block}
</style>


<!-- 아래처럼 다른 폼 사용해도 깔꿈하겠다-->
<!-- <form name="frm" action="proc" method="post">
<input type="hidden" name="dbjob" />
<input type="hidden" name="seq" />
<input type="hidden" name="sort_no" value="">
<input type="hidden" name="keyword" value="<?=$this->input->get('keyword')?>" />
</form>--> 

<!-- script 추가 -->
<script type="text/javascript">
//순서변경
function sortGoods(a,b,c){
	 
	with(document.fboardlist){
 
		dbjob.value = a;
		seq.value = b;
		sort_no.value = c;
		action = "/bbs/list_update_num.php"
		target = "ifrm-proc";
		submit();
		//action = ""; 
		//target = "";
	}
}

</script>
  
            
{% endhighlight %}


# 3. 이미지 업로드 (www/img/icon01.png)
<figure>
	<img src="/assets/img/icon01.png">
</figure>


# 4. Codeigniter - Controller에서 사용 
{% highlight php%}
    /**
	 * 상품 정렬 처리
	 * 
	 */
     
	function productSortProc() 
	{
		//검색
		$page  = $this->input->post('page',true);
		$dbjob  = $this->input->post('dbjob',true);
		$sort_no  = $this->input->post('sort_no',true);
		$seq  = $this->input->post('seq',true);
		$cate = $this->input->post("cate",true);
		$keyname = $this->input->post("keyname",true);
		$keyword = $this->input->post("keyword",true);
		
		$p[] = 'cate='.$cate;
		$p[] = 'keyname='.$keyname;
		$p[] = 'keyword='.urlencode($keyword);
		$param = '&'.implode('&',$p);

			
		//상품 순서정렬 조건절
		$_where = array();
		if($cate) $_where[] = " cate='$cate' ";
		if($keyname && $keyword) $_where[] = $keyname." LIKE '%".$keyword."%'";
		$where = (sizeof($_where)>0)?' AND '.implode(' AND ',$_where):'';
		
		unset($config);		
		$config['where'] = array('seq'=>$seq);
		$config['table'] = 'mpl_product';
		$config['dbjob'] = $dbjob;

		switch($dbjob)
		{
		case('down'):
		$msg = '수정되었습니다.';
		$config['data'] = null;
		$query = $this->db->query('SELECT seq,sort_no FROM '.$config['table'].' WHERE sort_no > '.$sort_no.$where.'  ORDER BY sort_no LIMIT 1');
   
		if($query->num_rows() > 0){
			
			$row = $query->row_array();
			$this->db->where('seq',$seq);
			$this->db->update($config['table'],array('sort_no'=>$row['sort_no']));

			$this->db->where('seq',$row['seq']);
			$this->db->update($config['table'],array('sort_no'=>$sort_no));
		}
		break;
		case('up'):
			$msg = '수정되었습니다.';
			$config['data'] = null;
			$query = $this->db->query('SELECT seq,sort_no FROM '.$config['table'].' WHERE sort_no < '.$sort_no.$where.'  ORDER BY sort_no DESC LIMIT 1 ');

			if($query->num_rows() > 0){
				$row = $query->row_array();
				$this->db->where('seq',$seq);
				$this->db->update($config['table'],array('sort_no'=>$row['sort_no']));
				$this->db->where('seq',$row['seq']);
				$this->db->update($config['table'],array('sort_no'=>$sort_no));	
			}
		break;
		}
		modal_script(json_encode(array('msg'=>$msg,'url'=>base_url('/product/manager/productList?language='.$this->language.'&page='.$page.$param))));

	}
{% endhighlight%}
