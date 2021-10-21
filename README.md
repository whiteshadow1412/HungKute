<%--
//******************************************************************************
// Copyright 2002 by KYUSHU ELECTRIC POWER CO.,INC All Rights Reserved.
//
// システム名      　：電力輸送部門システム
// サブシステム名    ：保全業務　業務実績管理
// 画面名           ：個人工数一覧検索
// ソースファイル名   ：FRMCGS003.jsp
//
//==============================================================================
// version    変更日         変更者              変更内容
//------------------------------------------------------------------------------
// 1.00       2005.07.06     Osamu.Hasegawa      RM-001 新規作成
// 1.01       2005.09.22     Kenjirou.Nakashima  RM-070 必須項目の”必須”表示削除
// 2.00       2019.05.29     K.Warashina         FCO-843 Excel出力時に新規ウィンドウが立ち上がる問題対応
//******************************************************************************
--%>
<%@ page contentType="text/html;charset=Windows-31J" %>
<%--　パッケージのインポート　--%>
<%@ page import="java.util.Map" %>
<%@ page import="java.util.Iterator"%>
<%@ page import="java.util.Hashtable" %>
<%@ page import="java.util.List" %>
<%@ page import="psmof.psmofwp.common.Code" %>
<%@ page import="psmof.psmofwp.fframe.FrameUtil" %>
<%@ page import="jp.co.toshiba.common.servlet.*" %>

<jsp:useBean id="proc" class="psmof.psmofwp.fframe.LogicExecutor" scope="request">
</jsp:useBean>
<%-- サーブレットからのデータ受信処理開始 --%>
<%
	Map  map = (Map)proc.getOutContainer();
%>
<%-- サーブレットからのデータ受信処理終了 --%>
<HTML>
<HEAD>
<LINK rel="stylesheet" type="text/css"
						href="<%=Code.CSS_FCO_PATH%>fcom_hozenfont12.css">
<STYLE type="text/css">
	.NormalSelect {
		width               : 490px;
		FONT                : 12pt 'ＭＳ ゴシック';
	}
	.DisableSelect {
		width               : 490px;
		background-color    : #cccccc;
		FONT                : 12pt 'ＭＳ ゴシック';
	}
	TD {
		FONT                : 12pt 'MS ゴシック'
	}
</STYLE>
<SCRIPT language="JavaScript" src="<%=Code.SCRIPT_CCO_PATH%>com_interface.js"></SCRIPT>
<SCRIPT language="JavaScript" src="<%=Code.SCRIPT_CCO_PATH%>com_check.js"></SCRIPT>
<SCRIPT language="JavaScript" src="<%=Code.SCRIPT_CCO_PATH%>com_util.js"></SCRIPT>
<SCRIPT language="JavaScript" src="<%=Code.SCRIPT_FCO_PATH%>fcom_hozen.js"></SCRIPT>
<SCRIPT language="JavaScript" src="<%=Code.SCRIPT_FCO_PATH%>fcom_inputChk.js"></SCRIPT>
<SCRIPT language="JavaScript" src="<%=Code.SCRIPT_FCO_PATH%>fcom_assist.js"></SCRIPT>
<SCRIPT language="JavaScript" src="<%=Code.SCRIPT_FCO_PATH%>fcom_search.js"></SCRIPT>

<SCRIPT language="JavaScript" src="<%=Code.SCRIPT_FWP_PATH%>FCO_Check.js"></SCRIPT>
<SCRIPT language="JavaScript" src="<%=Code.SCRIPT_FWP_PATH%>FCO_Rule.js"></SCRIPT>
<SCRIPT language="JavaScript" src="<%=Code.SCRIPT_FWP_PATH%>FCO_common.js"></SCRIPT>
<SCRIPT language="JavaScript" src="<%=Code.SCRIPT_FWP_CHECK_PATH%>FWP_Check.js"></SCRIPT>

<%@ include file = "../../../psmof/jsp/psmofco/RightClickCtrlInclude.jsp" %>
<%@ include file="../../jsp/psmofco/TitleBtnInclude.jsp" %>

<SCRIPT language="JavaScript">
<%--
<!-- メッセージ表示するためのファンクションを定義 LogicExecutorよりエラー内容が転送される -->
<%= proc.putJavaScriptMessageDefine() %>
--%>
<%-- 定数の定義開始 --%>
var DEFAULT_NENDO = <%=Code.FWP_HONNENDO_VALUE%>;  <%-- 年度の指定範囲定数 --%>
var FRAME_BUTTON  = "frame_button";     <%-- ボタンフレームの宣言　　　--%>
var FRAME_TITLE   = "frame_title";      <%-- タイトルフレームの宣言　　--%>
var FRAME_DATA    = "frame_data";       <%-- データ表示フレームの宣言　--%>
var m_ClickFlg = 1;                     <%-- ２度押しフラグ変数定義　　--%>
var checkfunc = new Array();            <%-- 読み込み関数定義　　　　　--%>
var strHelpGamenID = "FRMCGS003";       <%-- ヘルプボタン用画面ID定義　--%>

	checkfunc["NENDO"] = "FW01;FW02;";      <%-- FW1:数値チェック　FW2:年度の範囲チェック--%>
	checkfunc["TUKI"] = "FW01;FW03;";       <%-- FW3:月の範囲チェック --%>
<%-- 定数の定義終了 --%>
<%--
//******************************************************************************
// * 関数名    ：フレーム表示
// * 関数ID   ：SetTiteBtn()
// * 処理内容   ：タイトルフレームとボタンフレームの描画
// * 書式      ：<BODY onload="SetTitleBtn()">
// * 引数      ：なし
// * 戻り値    ：なし
//******************************************************************************
--%>
function SetTitleBtn(){
	var strButtonArea = "";
	<%-- 1でボタン描画　Excelボタン・添付資料・懸案事項管理・引継ぎメモ・ＯＫ・キャンセルの順--%>
	strButtonArea = FCO_MakeListButton('0','0','0','0','1','0');
	setBtnHTML();
	fcom_showTitle('年度別業務実績グラフ','FRMCGS003','業務実績管理',strTitleBtnArea);
	fcom_showBtn(strButtonArea);
	<%-- ボタンフレーム・タイトルフレームのDisable解除--%>
	fcom_SetBtnAble(FRAME_BUTTON,false);
	fcom_SetBtnAble(FRAME_TITLE,false);
}
<%--
//******************************************************************************
// * 関数名    ：キャンセルボタンクリック時の処理
// * 関数ID   ：Cancelbtn_click()
// * 処理内容  ：前画面を表示する。
// * 書式      ：< ･･･ onclick="Cancelbtn_click()">
// * 引数      ：なし
// * 戻り値    ：なし
//******************************************************************************
--%>
function Cancelbtn_click()
{
	<%-- ２度押し防止 --%>
	if (m_ClickFlg == 1){
		return;
	}
	m_ClickFlg = 1;

	<%-- 各種ボタンを使用不可にする。true :　使用不可 --%>
	com_SetAllObjectDisable();
	fcom_SetBtnAble(FRAME_BUTTON,true);
	fcom_SetBtnAble(FRAME_TITLE,true);

	<%-- １つ前のサーブレットへアクセス --%>
	document.main.FFRAME_MYURL.value = window.location;
	document.main.FFRAME_ROUTE.value = "cancel";
	document.main.action = "<%= (String)map.get("FFRAME_CANCEL_URL")%>";
	document.main.target = "_self";
	document.main.submit();
}
<%--
//******************************************************************************
// * 関数名    ：ＯＫボタン表示
// * 関数ID   ：Okbtn_click()
// * 処理内容  ：OKボタン押下時にメニュー画面に遷移する。
// * 書式      ：< ･･･ onclick="Okbtn_click()">
// * 引数      ：なし
// * 戻り値    ：なし
//******************************************************************************
--%>
function Okbtn_click() {
	Cancelbtn_click();
}
<%--
//******************************************************************************
// * 関数名    ：年度範囲チェック
// * 関数ID   ：NendoHanni(obj)
// * 処理内容   ：年度＋－２０年のチェック
// * 書式      ：<>
// * 引数      ：なし
// * 戻り値    ：true/false
//******************************************************************************
--%>
function NendoHanni(obj){
	var Honnendo = new Number(<%=(String)map.get("HON_NENDO")%>);
	var minNendo = Honnendo - DEFAULT_NENDO ;
	var maxNendo = Honnendo + DEFAULT_NENDO ;

	if (obj.value == "") {
		return true;
	}
	if ((obj.value > maxNendo) || (obj.value < minNendo)){
		var MESSAGE_FWPCMW2006 ="";
		<%--警告メッセージID：FWPCMW2006--%>
		MESSAGE_FWPCMW2006 = "年度の値が範囲をはずれています。\n適切な値を入力してください。\n" +
				  "（適応範囲：" +
				  minNendo +  "-" +
				  maxNendo + "）";
		obj.style.color = "#FF0000";
		com_OpnWarningDialogMsg("警告メッセージ",MESSAGE_FWPCMW2006);
		obj.focus();
		return false ;
	}else{
		obj.style.color = "#000000";
		return true ;
	}
}
<%--
// *****************************************************************************
// * 関数名         ：情報取得用ウィンドウ表示関数
// * 関数 ID        ：OpenWindow()
// * 処理内容       ：情報取得用のウィンドウを表示します。
// * 書式           ：<･･･ onclick="OpenWindow()">
// * 引数           ：i（機能判別用ナンバー）
// * 戻り値         ：なし
// *****************************************************************************
--%>
function OpenWindow(iNo){

	<%-- ２度押し防止 --%>
	if (m_ClickFlg == 1){
		return;
	}
	m_ClickFlg = 1;

<%  String strSetubiKbn = (String) map.get("DIFF_SETUBI_KBNCD"); %>

	switch (iNo){
	<%-- 作業担当箇所 --%>
	case 1:
		<% String strTantou = "document.main"; %>
		fcom_OpenSagyouTantou(<%= strTantou %>,document.main.SAGYO_TANTO_KANRI_ID.value,null,true);
		break;



	}
}
<%--
// *****************************************************************************
// * 関数名         ：作業担当箇所情報取得関数
// * 関数 ID        ：SelectSagyouTantou(i1, i2, i3, i4, i5, i6)
// * 処理内容       ：作業担当箇所の情報取得を行います。
// * 書式           ：<･･･ onclick="SelectSagyouTantou(i1, i2, i3, i4, i5, i6)">
// * 引数           ：i1（管理ID）
//                  ：i2（機関ID）
//                  ：i3（機関コード）
//                  ：i4（機関名称１）
//                  ：i5（機関名称２）
//                  ：i6（機関名称３）
// * 戻り値         ：なし
// *****************************************************************************
--%>
function SelectSagyouTantou(i1, i2, i3, i4, i5, i6) {
	if (i1 == "") {
		document.main.SAGYO_TANTO_ID.value = "<%=Code.FWP_ALL_ZERO%>";
		document.main.SAGYO_TANTO_NAME.value = "";
		document.main.SAGYO_TANTO_KANRI_ID.value = "";
	} else {
		document.main.SAGYO_TANTO_ID.value = i2;
		document.main.SAGYO_TANTO_NAME.value = i5;
		document.main.SAGYO_TANTO_KANRI_ID.value = i1;
	}
}
<%--
// *****************************************************************************
// * 関数名         ：サブウィンドウ終了時実行関数
// * 関数 ID        ：AfterCloseWindow()
// * 処理内容       ：サブウィンドウが閉じられた後に実行されます。
// * 書式           ：<･･･ onclick="AfterCloseWindow()">
// * 引数           ：なし
// * 戻り値         ：なし
// *****************************************************************************
--%>
function AfterCloseWindow() {
	m_ClickFlg = 0;
}
<%--
// *****************************************************************************
// * 関数名			：ＥＸＣＥＬボタンクリック
// * 関数 ID		：ExcelWindowOpen()
// * 処理内容		：ＥＸＣＥＬボタンクリック時にサブシステムへ遷移する。
// * 書式			：<･･･ onclick="Excelbtn_click()">
// * 引数			：なし
// * 戻り値			：なし
// *****************************************************************************
--%>
function ExcelWindowOpen() {

	<%-- 更新ターゲットを元に戻す --%>
	document.main.target = "frame_data";

// [MOD] START 2019.05.29 K.Warashina R2.00
// 	window.open("/webapp/psmo/servlet/psmof.psmofrm.ess.HozenRMGyomuGraphEssServlet" + "?file=/Excel出力.xls", "Excel");
	fcom_ExcelDownload("/webapp/psmo/servlet/psmof.psmofrm.ess.HozenRMGyomuGraphEssServlet" + "?file=/Excel出力.xls");
// [MOD] E N D 2019.05.29 K.Warashina R2.00
	m_ClickFlg = 0;
}
<%--
// *****************************************************************************
// * 関数名 		：Excel出力ボタンクリック
// * 関数 ID		：Excelbtn_click()
// * 処理内容		：Excel出力クリック時に隠しフレームへサブミットする。
// * 書式			：<･･･ onclick="Excelbtn_click()">
// * 引数			：なし
// * 戻り値 		：なし
// *****************************************************************************
--%>
function Excelbtn_click() {
	document.main.FFRAME_MYURL.value = window.location;
	document.main.FFRAME_ROUTE.value = "next";
	document.main.target = "frame_work";
	document.main.action = "/webapp/psmo/servlet/psmof.psmofrm.servlet.FRMCGS003ExcelServlet";
	document.main.submit();

}
<%--
// *****************************************************************************
// * 関数名 		：Excel出力アンカークリック
// * 関数 ID		：Excelank_click()
// * 処理内容		：Excel出力クリック時に隠しフレームへサブミットする。
// * 書式			：<･･･ onclick="Excelank_click()">
// * 引数			：なし
// * 戻り値 		：なし
// *****************************************************************************
--%>
function Excelank_click(strSosikiId) {
	document.main.SAGYO_TANTO_ID.value = strSosikiId;
	Excelbtn_click();
}

</SCRIPT>
</HEAD>
<BODY class="FCOlistBody12" onload = "SetTitleBtn(),fcom_SessionTimer(<%= Code.FWP_SESSION_MILLISECOND %>,<%= Code.FWP_SESSION_NUMBER %>)">
<FORM name="main" method="post" onsubmit="return FCO_SubmitFnc()">
<%@ include file="../../jsp/psmofco/SessionPreCondition.jsp" %>
<CENTER>
<BR>
<DIV style="width:800;" align="left">
<TABLE border="0">
<TBODY>
<%-- 年度のテキストボックス表示開始 --%>
<TR>
	<TD>年度</TD>
	<TD>
		<INPUT type="text" name="NENDO" value="<%= (String)map.get("NENDO") %>"
		size="14" maxlength="4" tabindex="1" class ="FCOneedTextR12" onfocus = "fco_ExcFunc(this);" onblur ="fco_SetFunc(this);"><%--DEL START 2005.08.26 (Rev.1.01) kenjirou.nakashima（※必須）--%>
	</TD>
</TR>
<%-- 年度のテキストボックス表示終了 --%>

<%-- 組織の表示開始 --%>
	<TR>
		<TD>店所名</TD>
		<TD class='FCOtableTdL' style="font-size:12pt;">
			<SELECT class="FCOneedSelectBoxL12" name="SOSIKI_ID" style="width:400;">
			<%-- 組織のコードとデータ表示開始--%>
			<%
				List listSelectAllKasho = (List) map.get("LISTALLKASHO");
				if (listSelectAllKasho != null){
					Map mapData = null;
					String strCode = "";
					String strData = "";
					String strSelected = "";
					Iterator iteKasho = listSelectAllKasho.iterator();
					while (iteKasho.hasNext()){
						mapData = (Map) iteKasho.next();
						strCode = (String) mapData.get("KIKAN_ID").toString();
						strData = (String) mapData.get("KIKAN_NAME");
						if (strCode.equals(map.get("DIFF_SOSIKI_ID"))){
							strSelected = "selected";
						}else{
							strSelected = "";
						}
			%>
			<OPTION value = "<%=strCode%>" <%=strSelected%>><%=strData%></OPTION>
			<%}}%>
			<%-- 組織のコードとデータ表示終了--%>
			</SELECT>
		</TD>
	</TR>
<%-- 組織の表示終了 --%>
	<TR>
		<TD>パスワード</TD>
		<TD class='FCOtableTdL' style="font-size:12pt;">
			<INPUT type=PASSWORD class="FCOneedSelectBoxL12" name="SOSIKI_PASS" style="width:200;">
			<INPUT style="text-align:center;font:normal normal 12pt 'ＭＳ ゴシック'" type="button" name="TenshoKakuteiBtn" value="確定" onClick="">
		</TD>
	</TR>

					<TR>
						<TD>業務分類</TD>
						<TD class='FCOtableTdL' style="font-size:12pt;">
							<SELECT class="FCOneedSelectBoxL12" name="GYOMU_BUNRUI_CD" style="width:200;">
							<%-- 業務分類のコードとデータ表示開始--%>
							<%
								List listSelectGyomu = (List) map.get("LISTGYOMU");
								if (listSelectGyomu != null){
									Map mapData = null;
									String strCode = "";
									String strData = "";
									String strSelected = "";
									Iterator iteGyomu = listSelectGyomu.iterator();
									while (iteGyomu.hasNext()){
										mapData = (Map) iteGyomu.next();
										strCode = (String) mapData.get("GYOMU_BUNRUI_CD");
										strData = (String) mapData.get("GYOMU_BUNRUI_NAME");
										if (strCode.equals(map.get("DIFF_GYOMU_BUNRUI_CD"))){
											strSelected = "selected";
										}else{
											strSelected = "";
										}
							%>
							<OPTION value = "<%=strCode%>" <%=strSelected%>><%=strData%></OPTION>
							<%}}%>
							<%-- 業務分類のコードとデータ表示終了--%>
							</SELECT>
						</TD>
					</TR>

<%-- 作業担当箇所の表示開始 --%>
<TR>
		<%-- 作業担当箇所Disable判定開始--%>
		<%
			String strSagyoTanto = (String)map.get("DIFF_SAGYO_TANTO_ID");
			if (strSagyoTanto == null || strSagyoTanto.length() == 0) {
				strSagyoTanto = Code.FWP_ALL_ZERO;
			}
			String strSagyoTantoCd = (String)map.get("DIFF_SAGYO_TANTO_CD");
			if (strSagyoTantoCd == null || strSagyoTantoCd.length() == 0) {
				strSagyoTantoCd = "";
			}
			String strSagyoTantoName = (String)map.get("SAGYO_TANTO_NAME");
			if(strSagyoTantoName == null || strSagyoTantoName.equals(Code.FWP_ALL_NAME)) {
				strSagyoTantoName = "";
			}
			String strKashoList = (String)map.get("LISTKASHO").toString();
			List listKashoList = (List) map.get("LISTKASHO");
		%>
		<%-- 作業担当箇所Disable判定終了--%>
		<%-- 作業担当箇所のコードとデータ表示開始--%>
		<TD><INPUT type="hidden" name="SAGYO_TANTO_ID" value="<%= strSagyoTanto %>">
		    <INPUT type="hidden" name="SAGYO_TANTO_CD" value="<%= strSagyoTantoCd %>">
		</TD>
</TR>
<%-- 作業担当箇所の表示終了 --%>

</TBODY>
</TABLE>
<%-- デバッグ用の組織名一覧表示開始 --%>
<HR>デバッグ用<BR>
    			<%  if (listKashoList.size()> 0) { %>
					<%
                        Iterator iteKashoList = listKashoList.iterator();
						while (iteKashoList.hasNext()) {
							Map mapData = (Map) iteKashoList.next();
					%>
						<%   String strKikanId = mapData.get("KIKAN_ID").toString().trim();%>
						<%   String strKikanName = mapData.get("KIKAN_NAME").toString().trim();%>
						<a href="javascript:Excelank_click(<%= strKikanId %>)"><%= strKikanName %></a><br>
                    <%
                        }
                    %>
               <% }%>
<HR>
<%-- デバッグ用の組織名一覧表示終了 --%>
<%-- Hidden項目の記述開始--%>
	<INPUT type = "hidden" name ="FFRAME_MYURL">
	<INPUT type = "hidden" name ="FFRAME_MYGID" value = "FRMCGS003">
	<INPUT type = "hidden" name ="FFRAME_ROUTE">


	<INPUT type = "hidden" name ="DIFF_SAGYO_TANTO_ID">
<%-- Hidden項目の記述終了--%>
</DIV>
</CENTER>
</FORM>
</BODY>
<SCRIPT>
 m_ClickFlg = 0;
</SCRIPT>
</HTML>
<!-- $Id: FRMCGS003.jsp,v 1.8 2005/09/26 00:40:03 NMotoo Exp $ -->
