---
layout: post
title: "[PHP] BOARD Form & 입력값 검증하기 "
date: 2021-08-06
excerpt: "Form & 입력값 검증하기"
tags: [PHP, JavaScript, formValid, Codeigniter, Mail, 유동변수, 메일]
comments: false
php: true
feature: /assets/img/php_logo.jpg
---


# 1. View
{% highlight php %}
<div class="boardForm">
	<form name="frm" method="post" action="/front/proc">
	<input type="hidden" name="dbjob" value="i" />
		<div class="inputbox">
			<input type="text" id="name" name="name" placeholder="이름" /> 
			<input type="text" id="hp" name="hp"  maxlength="11" placeholder="번호" required="">
			<!--<input type="tel" id="hp" name="hp"  placeholder="번호" maxlength="11" /> 번호만 입력가능--> 
			<input type="text"  id="email" name="email" placeholder="이메일"/>
			<div class="checkbox-custom checkbox-default">
				<input type="checkbox" id="chk_content" name="wr_1" value="A 프로그램 문의">
				<label for="chk1">A 프로그램 문의</label>
			</div>
			<div class="checkbox-custom checkbox-default">
				<input type="checkbox" id="chk_content" name="wr_2" value="B 제품 문의">
				<label for="chk2">B 제품 문의</label>
			</div>
			<div class="checkbox-custom checkbox-default">
				<input type="checkbox" id="chk_content" name="wr_3" value="C 행사 문의">
				<label for="chk3">C 행사 문의</label>
			</div>
			<div class="checkbox-custom checkbox-default">
				<input type="checkbox" id="chk_content" name="wr_4" value="D 기타 문의">
				<label for="chk4">D 기타 문의</label>
			</div>
			<input type="checkbox" name="agree"  id="agree" placeholder="개인정보수집 및 이용안내"/>
		</div>
		<a href="javascript:frmValid();" class="submit">등록</a>
	</form>
</div>
 
<script>
function frmValid(){
	var chk_length = $("input:checkbox[id=chk_content]:checked").length;
	var regExpEmail = /^[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*@[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*\.[a-zA-Z]{2,3}$/i; //이메일
	var regExpHP = /^\d{3}\d{3,4}\d{4}$/; //핸드폰 
	var isnum = /^\d+$/.test(document.frm.hp.value);

	with(document.frm){
		if(!name.value){
			alert("이름을 입력해주세요");
			name.focus();
			return false;
		}

		if(!isnum){
			alert("연락처는 숫자만 입력 가능합니다.");
			return false;
		}

		if(!regExpHP.test(hp.value)){
			alert("연락처를 정확히 입력해주세요");
			hp.focus();
			return false;
		}

		if(!email.value){
			alert("이메일을 입력해주세요");
			email.focus();
			return false;
		}

		if(!regExpEmail.test(email.value)){
			alert("이메일 형식에 맞게 입력해주세요.");
			email.focus();
			return false;
		}

		if(chk_length < 1 ){
			alert("문의 내용은 적어도 하나는 선택해주세요.");
			wr_1.focus();
			return false;
		}

		if(!$("#agree").prop("checked")){
			alert("개인정보수집 및 이용안내에 동의 하셔야 합니다.");
			return false;
		}
		submit();
	}  
}
</script>

{% endhighlight %}

# 2. Controller (DB Insert, Codeigniter Mail)
{% highlight php %}

function proc()
{
	$dbjob = $this->input->post('dbjob',true);	
	$name = $this->input->post('name',true);	
	$hp = $this->input->post('hp',true);	
	$email = $this->input->post('email',true);	 
	$wr_1 = $this->input->post('wr_1',true);	 
	$wr_2 = $this->input->post('wr_2',true);	 
	$wr_3 = $this->input->post('wr_3',true);	 
	$wr_4 = $this->input->post('wr_4',true);	 
    
	$tmp_array = array($wr_1, $wr_2, $wr_3, $wr_4);
	for($i=0; $i<count($tmp_array); $i++){
		if($tmp_array[$i]){
			$content_array[] = $tmp_array[$i]; 
		}
	}

	$content = '';                             
	$content = implode( ' / ', $content_array );

	// 유동변수 필수값 체크하기                          
	$chk_array = array('name', 'hp', 'email');
	foreach($chk_array as $k => $v)
	{  
		if(!${$v})
		{
		echo '{"result_code":"05","result_msg":"변수오류"}';
		exit;
		} 
	}
                      
	$config = array(
		'dbjob'=>$dbjob,
		'table'=>'mpl_inquiry',
	);

	$msg = '등록되었습니다.'; 
	$config['data'] = array(
			'name' => $name, 
			'hp' => $hp,
			'email' => $email,
			'content' => $content,
			'regist_date'=> date("Y-m-d H:i",time()),
	);
 
	$data = array(
		'name' => $name, 
		'hp' => $hp,
		'email' => $email,
		'content' => $content,
		'regist_date'=> date("Y-m-d H:i",time()),
	); 

	$this->common_model->_proc($config);    // insert 부분 

	//메일발송 
	$subject = '메일 제목입니다.';  
	$to = '1226betty@naver.com'; 
	$content = '메일 내용 입니다.';

	unset($config);
	$config = array(
		'subject'=>$subject,
		'to'=>$to,
		'from'=>'test@test.com',
		// 'template'=>'subscribe.html',
		'data'=>array('CATEGORY'=> $subject,'EMAIL'=>$email, 'TITLE'=>$title, 'CONTENT'=> nl2br($content)), 
	);
	send_mail($config);
	  
	jalert($msg, '/'); 
}

{% endhighlight %}
