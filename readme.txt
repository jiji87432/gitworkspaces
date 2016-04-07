/*
 * Copyright (C) 2014-2017 LS Information Technology Co. Ltd.
 * 
 * All right reserved.
 * 
 * This software is the confidential and proprietary information of LS Company of China. 
 * ("Confidential Information"). 
 * You shall not disclose such Confidential Information and shall use it only
 * in accordance with the terms of the contract agreement you entered into with LS inc.
 *
 * $Id: PayRollAction.java  2016年3月15日  yongji $
 * 
 * Create on 2016年3月22日 上午11:54:10
 * 
 * Description: 
 *
 */
package com.foorich.action.payroll;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import javax.servlet.http.HttpServletRequest;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import com.alibaba.dubbo.common.utils.StringUtils;
import com.alibaba.fastjson.JSONObject;
import com.common.constants.CommonConstants;
import com.common.constants.EnumConstants;
import com.foorich.util.Configuration;
import com.foorich.util.ExportExcel;
import com.foorich.util.RandomUtil;
import com.fullrich.entity.SysAccounts;
import com.opensymphony.webwork.ServletActionContext;
import com.yirupay.acc.infoquery.facade.IQueryUserFacade;
import com.yirupay.acc.infoquery.facade.dto.QueryAcctInfoRequest;
import com.yirupay.acc.infoquery.facade.dto.QueryAcctInfoResponse;
import com.yirupay.cls.manage.facade.IManageChargeInfoFacade;
import com.yirupay.cls.manage.facade.dto.QueryChargeInfoRequest;
import com.yirupay.cls.manage.facade.dto.QueryChargeInfoResponse;
import com.yirupay.common.utils.lang.DateUtil;
import com.yirupay.common.utils.lang.SeqUtils;
import com.yirupay.pcs.agent.facade.IAgentManagerFacade;
import com.yirupay.pcs.agent.facade.IAgentTranFacade;
import com.yirupay.pcs.agent.facade.dto.AgentBatchDetail;
import com.yirupay.pcs.agent.facade.dto.AuditAgentBatchRequest;
import com.yirupay.pcs.agent.facade.dto.AuditAgentBatchResponse;
import com.yirupay.pcs.agent.facade.dto.QueryAgentBatchRequest;
import com.yirupay.pcs.agent.facade.dto.QueryAgentBatchResponse;
import com.yirupay.pcs.agent.facade.dto.QueryBatchDetailRequest;
import com.yirupay.pcs.agent.facade.dto.QueryBatchDetailResponse;
import com.zframework.core.base.BaseAction;
import com.zframework.core.util.Page;
import com.zframework.system.service.IService;

/**
 *
 *Creating a new branch is quick AND simple.
 Switched to a new branch 'dev'
 * <b>类说明：代发工资管理git测试Creating a new branch is quick</b>
 * 
 * <p>
 * <b>详细描述：</b>
 * 
 * <p>
 * @author yongji
 * @since 2016年3月22日
 */
public class PayRollAction extends BaseAction {

    /**
     * 
     * @since 1.0.0
     */

    private static final long serialVersionUID = 526142840368979724L;

    private static Logger logger = LoggerFactory.getLogger(PayRollAction.class);
    private Page page;
    private IService service;
    private IAgentManagerFacade agentManagerFacade;
    private IAgentTranFacade agentTranFacade;
    private IQueryUserFacade queryUserFacade;
    private IManageChargeInfoFacade manageChargeInfoFacade;
    private String userNo;
    private String merNo;
    private String batNo;
    private String batSta;
    private String merJnlNo;
    private String cardNo;
    private String payeeName;
    private String tranSta;
    private String merRevSta;
    private String comments;
    private String chargeNo;
    private String audStatus;
    private String agentDate;

    public Page getPage() {
        return page;
    }

    public void setPage(Page page) {
        this.page = page;
    }

    public IService getService() {
        return service;
    }

    public void setService(IService service) {
        this.service = service;
    }

    public IAgentManagerFacade getAgentManagerFacade() {
        return agentManagerFacade;
    }

    public void setAgentManagerFacade(IAgentManagerFacade agentManagerFacade) {
        this.agentManagerFacade = agentManagerFacade;
    }

    public IAgentTranFacade getAgentTranFacade() {
        return agentTranFacade;
    }

    public void setAgentTranFacade(IAgentTranFacade agentTranFacade) {
        this.agentTranFacade = agentTranFacade;
    }

    public IQueryUserFacade getQueryUserFacade() {
        return queryUserFacade;
    }

    public void setQueryUserFacade(IQueryUserFacade queryUserFacade) {
        this.queryUserFacade = queryUserFacade;
    }

    public IManageChargeInfoFacade getManageChargeInfoFacade() {
        return manageChargeInfoFacade;
    }

    public void setManageChargeInfoFacade(IManageChargeInfoFacade manageChargeInfoFacade) {
        this.manageChargeInfoFacade = manageChargeInfoFacade;
    }

    public String getUserNo() {
        return userNo;
    }

    public void setUserNo(String userNo) {
        this.userNo = userNo;
    }

    public String getMerNo() {
        return merNo;
    }

    public void setMerNo(String merNo) {
        this.merNo = merNo;
    }

    public String getBatNo() {
        return batNo;
    }

    public void setBatNo(String batNo) {
        this.batNo = batNo;
    }

    public String getBatSta() {
        return batSta;
    }

    public void setBatSta(String batSta) {
        this.batSta = batSta;
    }

    public String getTranSta() {
        return tranSta;
    }

    public void setTranSta(String tranSta) {
        this.tranSta = tranSta;
    }

    public String getMerJnlNo() {
        return merJnlNo;
    }

    public void setMerJnlNo(String merJnlNo) {
        this.merJnlNo = merJnlNo;
    }

    public String getCardNo() {
        return cardNo;
    }

    public void setCardNo(String cardNo) {
        this.cardNo = cardNo;
    }

    public String getPayeeName() {
        return payeeName;
    }

    public void setPayeeName(String payeeName) {
        this.payeeName = payeeName;
    }

    public String getMerRevSta() {
        return merRevSta;
    }

    public void setMerRevSta(String merRevSta) {
        this.merRevSta = merRevSta;
    }

    public String getComments() {
        return comments;
    }

    public void setComments(String comments) {
        this.comments = comments;
    }

    public String getChargeNo() {
        return chargeNo;
    }

    public void setChargeNo(String chargeNo) {
        this.chargeNo = chargeNo;
    }

    public String getAudStatus() {
        return audStatus;
    }

    public void setAudStatus(String audStatus) {
        this.audStatus = audStatus;
    }

    public String getAgentDate() {
        return agentDate;
    }

    public void setAgentDate(String agentDate) {
        this.agentDate = agentDate;
    }

    public PayRollAction() {
    }

    /**
     * 
     * <b>Description:</b>代发工资查询<br/>
     * <b>Author:</b>yongji<br/>
     * <b>CreateTime:</b>2016年3月22日下午2:02:08<br/>
     * 
     * @return String
     * @throws
     * @since  1.0.0
     */
    public String payrollQuery() {
        return this.payrollBatchQuery();
    }

    /**
     * 
     * <b>Description:</b>代发工资批次查询<br/>
     * <b>Author:</b>yongji<br/>
     * <b>CreateTime:</b>2016年3月22日下午2:02:29<br/>
     * 
     * @return String
     * @throws
     * @since  1.0.0
     */
    public String payrollBatchQuery() {
        if (!isLogin()) {
            setMessage(login);
            return ERROR;
        }
        HttpServletRequest req = ServletActionContext.getRequest();
        QueryAgentBatchRequest request = new QueryAgentBatchRequest();
        merNo = req.getParameter("merNo");
        batNo = req.getParameter("batNo");
        batSta = req.getParameter("batSta");
        request.setReqSysDate(new Date());
        request.setFlowNo(SeqUtils.getUniqueID());
        request.setOperSys(CommonConstants.BOSS_SYS_CODE);
        if (StringUtils.isNotEmpty(merNo)) {
            request.setMerNo(merNo);
        }
        if (StringUtils.isNotEmpty(batNo)) {
            request.setBatNo(batNo);
        }
        if (StringUtils.isNotEmpty(batSta)) {
            request.setBatSta(batSta);
        }
        /** 商户审核状态 0：待审核 始 1：审核成功 9：审核拒绝 */
        request.setMerRevSta("1");
        if (null == page) {
            page = new Page();
        }
        try {
            logger.info("代付批次查询请求DTO：{}", (null != request ? JSONObject.toJSONString(request) : request));
            QueryAgentBatchResponse resp = agentTranFacade.queryAgentBatch(request);
            logger.info("代付批次查询返回结果：{}", (null != resp ? JSONObject.toJSONString(resp) : resp));
            if (null != resp && resp.getRespCode().equals(CommonConstants.PCS_SUCCESS_NO)) {
                page = service.getList(resp.getBatchList(), resp.getTotalCount(), page);
                return SUCCESS;
            } else {
                if (null != resp && null != resp.getRespCodeMemo()) {
                    setMessage("<script>alert(\" " + resp.getRespCodeMemo() + "\");history.go(-1);</script>");
                } else {
                    setMessage("<script>alert(\"PCS系统返回错误，请联系管理员!\");history.go(-1);</script>");
                }
                return ERROR;
            }
        } catch (Exception e) {
            e.printStackTrace();
            return ERROR;
        }
    }

    /**
     * 
     * <b>Description:</b>批次明细查询<br/>
     * <b>Author:</b>yongji<br/>
     * <b>CreateTime:</b>2016年3月29日上午11:25:59<br/>
     * 
     * @return String
     * @throws
     * @since  1.0.0
     */
    public String payrollBatchQueryDetail() {
        if (!isLogin()) {
            setMessage(login);
            return ERROR;
        }
        HttpServletRequest req = ServletActionContext.getRequest();
        QueryBatchDetailRequest request = new QueryBatchDetailRequest();
        batNo = req.getParameter("batNo");
        merJnlNo = req.getParameter("merJnlNo");
        cardNo = req.getParameter("cardNo");
        payeeName = req.getParameter("payeeName");
        tranSta = req.getParameter("tranSta");
        request.setReqSysDate(new Date());
        request.setFlowNo(SeqUtils.getUniqueID());
        request.setOperSys(CommonConstants.BOSS_SYS_CODE);
        if (StringUtils.isNotEmpty(batNo)) {
            request.setBatNo(batNo);
        }
        if (StringUtils.isNotEmpty(merJnlNo)) {
            request.setMerJnlNo(merJnlNo);
        }
        if (StringUtils.isNotEmpty(cardNo)) {
            request.setCardNo(cardNo);
        }
        if (StringUtils.isNotEmpty(payeeName)) {
            request.setPayeeName(payeeName);
        }
        if (StringUtils.isNotEmpty(tranSta)) {
            request.setTranSta(tranSta);
        }
        if (null == page) {
            page = new Page();
        }
        try {
            logger.info("批次明细查询请求DTO：{}", (null != request ? JSONObject.toJSONString(request) : request));
            QueryBatchDetailResponse resp = agentManagerFacade.queryAgentDetail(request);
            logger.info("批次明细查询返回结果：{}", (null != resp ? JSONObject.toJSONString(resp) : resp));
            if (null != resp && resp.getRespCode().equals(CommonConstants.PCS_SUCCESS_NO)) {
                page = service.getList(resp.getDetailLsit(), resp.getTotalCount(), page);
                return SUCCESS;
            } else {
                if (null != resp && null != resp.getRespCodeMemo()) {
                    setMessage("<script>alert(\" " + resp.getRespCodeMemo() + "\");history.go(-1);</script>");
                } else {
                    setMessage("<script>alert(\"PCS系统返回错误，请联系管理员!\");history.go(-1);</script>");
                }
                return ERROR;
            }
        } catch (Exception e) {
            e.printStackTrace();
            return ERROR;
        }
    }

    /**
     * 
     * <b>Description:</b>代发工资回盘文件上传<br/>
     * <b>Author:</b>yongji<br/>
     * <b>CreateTime:</b>2016年3月22日下午2:02:43<br/>
     * 
     * @return String
     * @throws
     * @since  1.0.0
     */
    public String payrollFileUpload() {
        return SUCCESS;
    }

    /**
     * 
     * <b>Description:</b>代发工资账户管理<br/>
     * <b>Author:</b>yongji<br/>
     * <b>CreateTime:</b>2016年3月22日下午2:02:58<br/>
     * 
     * @return String
     * @throws
     * @since  1.0.0
     */
    public String payrollManageAcc() {
        if (!isLogin()) {
            setMessage(login);
            return ERROR;
        }
        if (page == null) {
            page = new Page();
        }
        HttpServletRequest request = ServletActionContext.getRequest();
        userNo = request.getParameter("userNo");
        QueryAcctInfoRequest queryAcctInfoRequest = new QueryAcctInfoRequest();
        if (StringUtils.isNotEmpty(userNo)) {
            queryAcctInfoRequest.setUserNo(userNo);
        }
        queryAcctInfoRequest.setAcctType(EnumConstants.AccType.ENT_BALANCE);
        queryAcctInfoRequest.setFlowNo(SeqUtils.getUniqueID());
        queryAcctInfoRequest.setBizChnl(CommonConstants.BOSS_SYS_CODE);
        queryAcctInfoRequest.setReqSysDate(new Date());
        queryAcctInfoRequest.setOperSys(CommonConstants.BOSS_SYS_CODE);
        queryAcctInfoRequest.setPageIndex(page.getCurrentPageNu());
        queryAcctInfoRequest.setPageFlag(true);
        queryAcctInfoRequest.setPageSize(page.getPageSize());
        try {
            logger.info("代发工资账户管理请求DTO:", (null != queryAcctInfoRequest ? JSONObject.toJSONString(queryAcctInfoRequest)
                    : queryAcctInfoRequest));
            QueryAcctInfoResponse resp = queryUserFacade.queryAcctInfo(queryAcctInfoRequest);
            logger.info("代发工资账户管理请求DTO:", (null != resp ? JSONObject.toJSONString(resp) : resp));
            if (null != resp && resp.getRespCode().equals(CommonConstants.ACC_SUCCESS_NO)) {
                page = service.getPageFromList(resp.getAccInfoList(), resp.getTotalCount(), page);
                return SUCCESS;
            } else {
                if (null != resp && null != resp.getRespCodeMemo()) {
                    setMessage("<script>alert(\" " + resp.getRespCodeMemo() + "\");history.go(-1);</script>");
                } else {
                    setMessage("<script>alert(\"ACC系统返回错误，请联系管理员!\");history.go(-1);</script>");
                }
                return ERROR;
            }
        } catch (Exception e) {
            e.printStackTrace();
            setMessage("<script>alert(\"ACC系统返回错误，请联系管理员!\");history.go(-1);</script>");
            return ERROR;
        }
    }

    /**
     * 
     * <b>Description:</b>代发工资账户管理详情<br/>
     * <b>Author:</b>yongji<br/>
     * <b>CreateTime:</b>2016年3月29日下午6:11:37<br/>
     * 
     * @return String
     * @throws
     * @since  1.0.0
     */
    public String payrollManageAccDetail() {
        if (!isLogin()) {
            setMessage(login);
            return ERROR;
        }
        HttpServletRequest req = ServletActionContext.getRequest();
        userNo = req.getParameter("userNo");
        QueryChargeInfoRequest request = new QueryChargeInfoRequest();
        request.setFlowNo(SeqUtils.getUniqueID());
        request.setOperSys(CommonConstants.BOSS_SYS_CODE);
        request.setReqSysDate(new Date());
        if (StringUtils.isNotEmpty(userNo)) {
            request.setChargeNo(userNo);
        }
        /**审核状态 0：未审核，1：审核通过，2：审核拒绝*/
        request.setAudStatus("1");
        if (page == null) {
            page = new Page();
        }
        try {
            logger.info("代发工资账户管理详情请求DTO:", (null != request ? JSONObject.toJSONString(request) : request));
            QueryChargeInfoResponse resp = manageChargeInfoFacade.queryChargeInfo(request);
            logger.info("代发工资账户管理详情请求DTO:", (null != resp ? JSONObject.toJSONString(resp) : resp));
            if (null != resp && resp.getRespCode().equals(CommonConstants.CLS_SUCCESS_NO)) {
                page = service.getPageFromList(resp.getMerChargeInfoList(), resp.getTotalCount(), page);
                return SUCCESS;
            } else {
                if (null != resp && null != resp.getRespCodeMemo()) {
                    setMessage("<script>alert(\" " + resp.getRespCodeMemo() + "\");history.go(-1);</script>");
                } else {
                    setMessage("<script>alert(\"CLS系统返回错误，请联系管理员!\");history.go(-1);</script>");
                }
                return ERROR;
            }
        } catch (Exception e) {
            e.printStackTrace();
            setMessage("<script>alert(\"CLS系统返回错误，请联系管理员!\");history.go(-1);</script>");
            return ERROR;
        }
    }

    /**
     * 
     * <b>Description:</b>代发工资审核查询<br/>
     * <b>Author:</b>yongji<br/>
     * <b>CreateTime:</b>2016年3月22日下午2:03:25<br/>
     * 
     * @return String
     * @throws
     * @since  1.0.0
     */
    public String payrollCheck() {
        return this.payrollBatchQuery();
    }

    /**
     * 
     * <b>Description:</b>代发工资审核<br/>
     * <b>Author:</b>yongji<br/>
     * <b>CreateTime:</b>2016年3月22日下午2:03:25<br/>
     * 
     * @return String
     * @throws
     * @since  1.0.0
     */
    public String payrollCheckDeal() {
        if (!isLogin()) {
            setMessage(login);
            return ERROR;
        }
        HttpServletRequest req = ServletActionContext.getRequest();
        AuditAgentBatchRequest request = new AuditAgentBatchRequest();
        batNo = req.getParameter("batNo");
        batSta = req.getParameter("batSta");
        comments = req.getParameter("comments");
        request.setReqSysDate(new Date());
        request.setFlowNo(SeqUtils.getUniqueID());
        request.setOperSys(CommonConstants.BOSS_SYS_CODE);
        if (StringUtils.isNotEmpty(batNo)) {
            request.setBatNo(batNo);
        }
        if (StringUtils.isNotEmpty(batSta)) {
            request.setBatSta(batSta);
        }
        if (StringUtils.isNotEmpty(comments)) {
            request.setComments(comments);
        }
        SysAccounts author = (SysAccounts) req.getSession().getAttribute("SYSaccount");
        String auditOperator = author.getOpercode();
        request.setOperId(auditOperator);
        if (null == page) {
            page = new Page();
        }
        try {
            logger.info("代发工资审核请求DTO：{}", (null != request ? JSONObject.toJSONString(request) : request));
            AuditAgentBatchResponse resp = agentManagerFacade.auditAgentBatch(request);
            logger.info("代发工资审核返回结果：{}", (null != resp ? JSONObject.toJSONString(resp) : resp));
            if (null != resp && resp.getRespCode().equals(CommonConstants.PCS_SUCCESS_NO)) {
                return SUCCESS;
            } else {
                if (null != resp && null != resp.getRespCodeMemo()) {
                    setMessage("<script>alert(\" " + resp.getRespCodeMemo() + "\");history.go(-1);</script>");
                } else {
                    setMessage("<script>alert(\"PCS系统返回错误，请联系管理员!\");history.go(-1);</script>");
                }
                return ERROR;
            }
        } catch (Exception e) {
            e.printStackTrace();
            return ERROR;
        }
    }

    /**
     * 
     * <b>Description:</b>导出代发详情<br/>
     * <b>Author:</b>yongji<br/>
     * <b>CreateTime:</b>2016年3月29日下午8:47:58<br/>
     * 
     * @return String
     * @throws
     * @since  1.0.0
     */
    @SuppressWarnings("static-access")
    public String exportFile() {
        HttpServletRequest req = ServletActionContext.getRequest();
        QueryBatchDetailRequest request = new QueryBatchDetailRequest();
        batNo = req.getParameter("batNo");
        request.setReqSysDate(new Date());
        request.setFlowNo(SeqUtils.getUniqueID());
        request.setOperSys(CommonConstants.BOSS_SYS_CODE);
        if (StringUtils.isNotEmpty(batNo)) {
            request.setBatNo(batNo);
        }
        if (null == page) {
            page = new Page();
        }
        try {
            logger.info("批次明细查询请求DTO：{}", (null != request ? JSONObject.toJSONString(request) : request));
            QueryBatchDetailResponse resp = agentManagerFacade.queryAgentDetail(request);
            logger.info("批次明细查询返回结果：{}", (null != resp ? JSONObject.toJSONString(resp) : resp));
            if (null != resp && resp.getRespCode().equals(CommonConstants.PCS_SUCCESS_NO)) {
                ExportExcel exportExcel = new ExportExcel();
                List<Object> listContent = new ArrayList<Object>();
                for (AgentBatchDetail agentBatchDetail : resp.getDetailLsit()) {
                    AgentBatchDetailDto detailDto = new AgentBatchDetailDto();
                    detailDto.setMerNo(Configuration.getInstance().getValue("merNo"));
                    detailDto.setBatNo(agentBatchDetail.getBatNo());
                    detailDto.setAgentDate(agentDate);
                    detailDto.setCoreJnlNo(agentBatchDetail.getCoreJnlNo());
                    detailDto.setMerFlag("01");
                    detailDto.setCity(agentBatchDetail.getCity());
                    detailDto.setOpenBank(agentBatchDetail.getOpenBank());
                    detailDto.setPayeeName(agentBatchDetail.getPayeeName());
                    detailDto.setCardNo(agentBatchDetail.getCardNo());
                    detailDto.setTranAmt(agentBatchDetail.getTranAmt());
                    detailDto.setPhnNo(agentBatchDetail.getPhnNo());
                    detailDto.setComments(agentBatchDetail.getComments());
                    listContent.add(detailDto);
                }
                String[] Title = { "商户号", "批次号", "商户日期", "订单号", "对公（00)/对私（01）", "开户银行所在城市", "开户银行", "账户名称", "账户号码",
                        "金额（元）", "手机号", "备注" };
                RandomUtil randomUtil = new RandomUtil();
                exportExcel.exportListObjToExcel(Configuration.getInstance().getValue("ExportExcelMerNoName") + "_"
                        + DateUtil.getCurrentDayString() + "_" + randomUtil.getRandomIntNum(6) + ".xls", Title,
                        listContent);
                return SUCCESS;
            } else {
                setMessage("<script>alert(\"导出文件出错，请联系管理员!\");history.go(-1);</script>");
                return ERROR;
            }
        } catch (Exception e) {
            e.printStackTrace();
            setMessage("<script>alert(\"导出文件出错，请联系管理员!\");history.go(-1);</script>");
            return ERROR;
        }
    }
}
