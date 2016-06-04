---
layout: post
title:  "状态模式、装饰器模式相结合的订单同步接口"
date:   2015-06-04 11:16:05
categories: 工作感悟
excerpt:  状态模式与装饰模式结合对订单扭转进行管理 扩展性、易读性、维护性均提高
---

* content
{:toc}

## 前言 
  &nbsp;&nbsp;前几个礼拜和一个客户对接，所有的订单信息均往一个接口进行同步（而且每次要判别5/6种订单状态来完成相应的业务逻辑处理），当时刚从学校回来就被排期，感觉被强奸了一样（应该是痛并快乐着吧，哈哈！），以至于不太熟悉业务，对整个同步接口没有考虑那么多细致的设计，再加上小组组长说先完成功能再说，我也就if  else 了。
大家也清楚if else带来就是业务逻辑混乱、维护难、扩展难，最重要的是在联调测试阶段找bug很费劲。于是我就想到利用状态模式和装饰模式对各种状态进行管理，下面是我对状态模式的一点拙见，有异议可评论，一同进步。<br/>

## 状态模式

#### 概述
&nbsp;&nbsp;当一个对象的内在状态改变时允许改变其行为，这个对象看起来像是改变了其类。 <br/>

#### 使用场景
&nbsp;&nbsp;主要解决的是当控制一个对象状态转换的条件表达式过于复杂时的情况。把状态的判断逻辑转移到表示不同的一系列类当中，可以把复杂的逻辑判断简单化。 <br/>

#### 模式涉及到角色
&nbsp;&nbsp;　  1 上下文环境（Context）：它定义了客户程序需要的接口并维护一个具体状态角色的实例，将与状态相关的操作委托给当前的Concrete State对象来处理，相当于状态管理类。

&nbsp;&nbsp;　　2 抽象状态（State）：定义一个接口或者抽象类以封装使用上下文环境的的一个特定状态相关的行为。

&nbsp;&nbsp;　　3 具体状态（Concrete State）：实现抽象状态定义的接口，完成相应状态逻辑处理。

#### 模式类图
![如图1](https://github.com/linhao007/linhao007.github.io/blob/master/css/pics/2016-06-04-statusPattern1.jpg?raw=true)<br/><br/>

## 订单同步接口状态模式实现

#### 设计类图
![如图2](https://github.com/linhao007/linhao007.github.io/blob/master/css/pics/2016-06-04-statusPattern2.jpg?raw=true)<br/><br/>

#### 设计解析
1、上下文环境（ManageStatus）:主要对客户同步过来的订单根据订单状态调用不同状态逻辑处理类，进而管理上下文，用于服务层调用。
<pre><code> 
     
    /**
     * Created by linhao007 on 2016/5/25.
     */
    @Component
    public class ManageStatus {
    	private static final Logger LOGGER = LoggerFactory.getLogger(ManageStatus.class);
    	@Autowired
    	private AcceptOrderStatus acceptOrderStatus;// 已接单
    	@Autowired
    	private CancelStatus cancelStatus;// 取消订单
    	@Autowired
    	private PayStatus payStatus;// 已支付
    	@Autowired
    	private ServicingStatus servicingStatus;// 服务中
    	@Autowired
    	private ServicedStatus servicedStatus;// 服务完成
    	@Autowired
    	private ValueAddStatus valueAddStatus;// 增值服务
    	/**
    	 * 将状态管理起来 根据不同的状态进行不同状态对象方法的响应
    	 */
    	public GuaResult reverseStatus(OrderStatusData orderStatusData) {
    		LOGGER.info("进入状态模式管理类  进行状态选择 呱呱状态：{}", orderStatusData.getNewStatus());
    		GuaResult myResult = null;
    		int newStatus = orderStatusData.getNewStatus();
    		switch (newStatus) {
    		case 10: // 订单取消
    			return cancelStatus.updateOrderStatus(orderStatusData);
    		case 50: // 已接单 ok
    			return acceptOrderStatus.updateOrderStatus(orderStatusData);
    		case 60:// 服务中
    			return servicingStatus.updateOrderStatus(orderStatusData);
    		case 65:// 增值服务
    			return valueAddStatus.updateOrderStatus(orderStatusData);
    		case 70:// 服务完成
    			return servicedStatus.updateOrderStatus(orderStatusData);
    		case 30:// 已支付
    			return payStatus.updateOrderStatus(orderStatusData);
    		default:
    			myResult = ResultWrapper.wrap(OrderStatusEnums.ERROR.getCode(), OrderStatusEnums.ERROR.getMessage());
    		}
    		return myResult;
    	}
    }

</code></pre>

2、抽象状态（Status）:定义一个接口来描述各个状态的一个特定状态行为。
<pre><code> 
    /**
     * Created by linhao007 on 2016/5/25.
     */
    public interface Status {
        /**
         * 修改订单信息
         * @param orderStatusData  订单管家类  对订单所处状态进行分类管理
         * @return
         */
        GuaResult updateOrderStatus(OrderStatusData orderStatusData);
    }
</code></pre>

3、装饰类（BaseStatusService）：将每个状态公共行为抽象出来成为父类方法，且继承status接口方便子类状态实现，并提供相应的抽象方法用于修饰子类，这样代码简洁易读。
<pre><code>
    public abstract class BaseStatusService implements Status {
    	private static final Logger LOGGER = LoggerFactory.getLogger(BaseStatusService.class);

    	@Autowired
    	protected MessageSend messageSend;

    	@Autowired
    	protected MailSendUtils mailSendUtils;

    	@Autowired
    	protected GuaTradeService guaTradeService;

    	@Autowired
    	protected TcTradeAgent tcTradeAgent;

    	@Autowired
    	protected CarWashOrderAgent carWashOrderAgent;

    	@Autowired
    	protected OrderAgent orderAgent;

    	protected List<CarWashOrderDto> carWashOrderDtos;

    	protected OrderDto orderDto;

    	public OrderDto getOrderDto() {
    		return orderDto;
    	}

    	public void setOrderDto(OrderDto orderDto) {
    		this.orderDto = orderDto;
    	}

    	public List<CarWashOrderDto> getCarWashOrderDtos() {
    		return carWashOrderDtos;
    	}

    	public void setCarWashOrderDtos(List<CarWashOrderDto> carWashOrderDtos) {
    		this.carWashOrderDtos = carWashOrderDtos;
    	}

    	/**
    	 * 每个订单都存在更新订单或者取消订单情况 公共部分抽取出来 子类如果更新订单或者取消订单有需求变更
    	 * 则重写updateOrderSuper或者cancelOrderSuper 否则默认使用正常父类需求的updateOrder或者cancelOrder
    	 * 子类只需要在重写方法中使用super关键字
    	 * @return
    	 */
    	public boolean updateOrder(List<CarWashOrderDto> carWashOrderDto) {
    		boolean orderDtoResult = false;
    		LOGGER.info("updateOrder 调用后台dubbo接口***** carWashOrderDto:{}", JSON.toJSONString(carWashOrderDto));
    		try {
    			orderDtoResult = carWashOrderAgent.updateOrder(carWashOrderDto);
    		} catch (Exception e) {
    			LOGGER.error("调用dubbo updateOrder client失败 CarWashOrderDto {}", carWashOrderDto);
    			LOGGER.error("error", e);
    		}
    		LOGGER.info("updateOrder 调用结束******* :{} ", orderDtoResult);
    		return orderDtoResult;
    	}

    	public boolean cancelOrder(OrderDto orderDto) {
    		boolean ordercancelResult = false;
    		LOGGER.info("取消订单 调用后台dubbo接口***** orderDto:{}", JSON.toJSONString(orderDto));
    		try {
    			ordercancelResult = orderAgent.cancelOrder(orderDto);
    		} catch (Exception e) {
    			LOGGER.error("调用dubbo cancelOrder client失败 orderDto {}", orderDto);
    			LOGGER.error("error", e);
    		}
    		LOGGER.info("cancelOrder 调用结束******* :{} ", ordercancelResult);
    		return ordercancelResult;
    	}

    	/**
    	 * 用于子类重写
    	 * @return
           */
    	public abstract boolean updateOrderSuper();

    	/**
    	 * 用于子类重写
    	 * @return
           */
    	public abstract boolean cancelOrderSuper();

    }

</code></pre>

4、具体实现类（由于状态居多，我举例AcceptOrderStatus、CancelStatus--这个状态下还细分客户爽约、协商撤单、超时取消于是又抽象出一个接口来管理这些状态)状态实现类只需要继承BaseStatusService在相应的方法中实现即可<br>

1)已接单状态处理类AcceptOrderStatus：
<pre><code>
    /**
     * 已接单 Created by linhu007 on 2016/5/25.
     */
    @Component
    public class AcceptOrderStatus extends BaseStatusService{
    	private static final Logger LOGGER = LoggerFactory.getLogger(AcceptOrderStatus.class);

    	@Override
    	public GuaResult updateOrderStatus(OrderStatusData orderStatusData) {
    		GuaResult guaResult = null;
    		this.setCarWashOrderDtos(orderStatusData.getCarWashOrderDtoListAll());
    		CarWashOrderDto carWashOrderDto = orderStatusData.getCarWashOrderDto();
    		if (!this.updateOrderSuper()) {
    			LOGGER.info("订单同步失败 主订单为orderid{} ", carWashOrderDto.getOrderId());
    			return ResultWrapper.wrap(OrderStatusEnums.ERR_GETORDER.getCode(),
    					OrderStatusEnums.ERR_GETORDER.getMessage());
    		}
    		// 预约中转换到已接单，将接单消息短信提醒用户
    		LOGGER.info("开始发送派单短信");
    		// 发送派单短信
    		LOGGER.info("准备开始发送派单短信》》》》》》》》》入参：mobile=" + carWashOrderDto.getUserMobile() + ",address="
    				+ carWashOrderDto.getServiceAddress() + ",服务人员" + carWashOrderDto.getServiceUserName() + ",服务人员电话="
    				+ carWashOrderDto.getServiceUserPhone());
    		messageSend.distributeSuccess(Long.parseLong(carWashOrderDto.getUserMobile()),
    				carWashOrderDto.getServiceAddress(), carWashOrderDto.getServiceUserName(),
    				carWashOrderDto.getServiceUserPhone());
    		return ResultWrapper.wrap(OrderStatusEnums.SUCCESS.getCode(), OrderStatusEnums.SUCCESS.getMessage());
    	}

    	/**
    	 * 默认调用父类更新订单方法
    	 * 
    	 * @return
    	 */
    	@Override
    	public boolean updateOrderSuper() {
    		return super.updateOrder(this.getCarWashOrderDtos());
    	}

    	/**
    	 * 默认调用父类取消订单方法
    	 * 
    	 * @return
    	 */
    	@Override
    	public boolean cancelOrderSuper() {
    		return super.cancelOrder(this.getOrderDto());
    	}

        public List<CarWashOrderDto> getCarWashOrderDtos() {
            return carWashOrderDtos;
        }

        public void setCarWashOrderDtos(List<CarWashOrderDto> carWashOrderDtos) {
            this.carWashOrderDtos = carWashOrderDtos;
        }

        public OrderDto getOrderDto() {
            return orderDto;
        }

        public void setOrderDto(OrderDto orderDto) {
            this.orderDto = orderDto;
        }

    }

</code></pre>

2、取消状态处理类---接口类：BaseCancelStatus
<pre><code>
    /**
     * Created by liinhao007 on 2016/5/25.
     */
    public interface BaseCancelStatus {
        /**
         * 超时取消
         * @param orderStatusData
         * @param carWashOrderDtos
         * @return
         */
        GuaResult overTimeCancel( OrderStatusData orderStatusData, List<CarWashOrderDto> carWashOrderDtos);

        /**
         * 客户爽约
         * @param orderStatusData
         * @param carWashOrderDtos
         * @return
         */
        GuaResult customerMiss( OrderStatusData orderStatusData, List<CarWashOrderDto> carWashOrderDtos);

        /**
         * 协商撤单
         * @param orderStatusData
         * @param carWashOrderDtos
         * @return
         */
        GuaResult consultCancel(OrderStatusData orderStatusData, List<CarWashOrderDto> carWashOrderDtos);

        /**
         * 客户取消
         * @param orderStatusData
         * @param carWashOrderDtos
         * @return
         */
        GuaResult customerCancel(OrderStatusData orderStatusData, List<CarWashOrderDto> carWashOrderDtos);

    }
</code></pre><br>

取消状态具体实现：CancelStatus
<pre><code>
/**
 * 取消状态 Created by linhu007 on 2016/5/25.
 */
@Component
public class CancelStatus extends BaseStatusService implements  BaseCancelStatus {
	private static final Logger LOGGER = LoggerFactory.getLogger(CancelStatus.class);
	private CarWashOrderDto masterOrder;
	private int oldStatus;
	private OrderStatusData orderStatusData;

	@Override
	public GuaResult updateOrderStatus( OrderStatusData orderStatusData) {
		this.setOrderStatusData(orderStatusData);
		this.setMasterOrder(orderStatusData.getCarWashOrderDto());
		List<OrderDto> list = orderStatusData.getList();
		this.setOldStatus(orderStatusData.getOldStatus());
		GuaResult myResult = null;
		this.setCarWashOrderDtos(orderStatusData.getCarWashOrderDtoListAll());
		int cancelReson = orderStatusData.getCancelReason();
		switch (cancelReson) {
		case 40: // 超时取消
			return this.overTimeCancel(orderStatusData, this.getCarWashOrderDtos());
		case 30: // 客户爽约
			return this.customerMiss( orderStatusData, this.getCarWashOrderDtos());
		case 50:// 协商撤单
			return this.consultCancel( orderStatusData, this.getCarWashOrderDtos());
		case 20:// 客户撤单
			return this.customerCancel(orderStatusData, this.getCarWashOrderDtos());
		default:
			myResult = ResultWrapper.wrap(OrderStatusEnums.ERROR.getCode(), OrderStatusEnums.ERROR.getMessage());
		}
		return myResult;
	}

	/**
	 * 超时取消
	 * @param orderStatusData
	 * @param carWashOrderDtos
     * @return
     */
	@Override
	public GuaResult overTimeCancel(OrderStatusData orderStatusData,
			List<CarWashOrderDto> carWashOrderDtos) {
		/*** 封装账单dto **/
		TcRespDto<List<TcTradeBillDto>> tradeBill = null;
		try {
			tradeBill = tcTradeAgent.getTradeBill(null,orderStatusData.getMasterOrderId() );
		} catch (Exception e) {
			LOGGER.error("error", e);
		}
		if (tradeBill == null) {
			return ResultWrapper.wrap(YesOrNoEnum.FAILD.code, YesOrNoEnum.FAILD.desc);
		}
		List<TcTradeBillDto> tcTradeBillDtos = tradeBill.getData();
		if (CollectionUtils.isEmpty(tcTradeBillDtos)) {
			throw new RuntimeException("***未找到账单**** " + JSON.toJSONString(tcTradeBillDtos));
		}
		Long referid = 0l;
		List<Long> orderids = Lists.newArrayList();
		for (int i = 0; i < tcTradeBillDtos.size(); i++) {
			orderids.add(tcTradeBillDtos.get(i).getOrderId());
		}
		referid = tcTradeBillDtos.get(0).getReferId();

		GuaTradeDto TradeDto = new GuaTradeDto();
		TradeDto.setOrderIds(orderids);
		TradeDto.setCarWashOrderId(referid);

		// .更新订单状态为取消状态,并添加取消原因
		if (!this.cancelOrderSuper()) {
			LOGGER.info("主订单取消失败 主订单为orderid{} ", this.getMasterOrder().getOrderId());
			return ResultWrapper.wrap(OrderStatusEnums.ERR_ORDER_CANCEL.getCode(),
					OrderStatusEnums.ERR_ORDER_CANCEL.getMessage());
		}

		String order_status_name = orderStatusData.getOrderStatusName();
		boolean flag = false;// 标识是退款短信还是超时未支付短信
		// 2.2 有一个未支付,则全部关闭账单
		for (int i = 0; i < tcTradeBillDtos.size(); i++) {
			TcTradeBillDto tcTradeBillDto = tcTradeBillDtos.get(i);
			if (tcTradeBillDto.getBillStatus().intValue() == BillStatusEnum.WAIT.getValue().intValue()) {
				GuaResult<GuaTradeDto> GuaResult = guaTradeService.closeBill(TradeDto);
				if (GuaResult.getCode() == YesOrNoEnum.FAILD.code) {
					LOGGER.error("*****关闭账单失败******* {}", JSON.toJSONString(TradeDto));
					return ResultWrapper.wrap(YesOrNoEnum.FAILD.code, YesOrNoEnum.FAILD.desc);
				}
				updateStatusAndPayStatus(tcTradeBillDtos, OrderPayStatusEnum.WEIHIFU.code, order_status_name);
				// 发送 超时未支付
				LOGGER.info("呱呱接口回调orderId={}:newState={},oladState={}", this.getMasterOrder().getOrderId(),
						this.getMasterOrder().getOrderStatus(), this.getOldStatus());
				// 超过15分钟 未支付 发送订单自动取消短信 通知用户
				LOGGER.info("开始发送超时未支付 短信");
				// 发送派单短信
				LOGGER.info("准备开始发送超时未支付 短信》》》》》》》》》入参：mobile=" + this.getMasterOrder().getUserMobile() + ",address="
						+ this.getMasterOrder().getServiceAddress() + ",服务人员"
						+ this.getMasterOrder().getServiceUserName() + ",服务人员电话="
						+ this.getMasterOrder().getServiceUserPhone());
				messageSend.cancelOvertime(Long.parseLong(this.getMasterOrder().getUserMobile()),
						this.getMasterOrder().getServiceAddress());
				return ResultWrapper.wrap(OrderStatusEnums.SUCCESS.getCode(), OrderStatusEnums.SUCCESS.getMessage());
			}
		}

		// 2.1 已支付 退款
		GuaResult<GuaTradeDto> GuaResult = guaTradeService.refundBill(TradeDto);
		updateStatusAndPayStatus(tcTradeBillDtos, OrderPayStatusEnum.YIZHIFU.code, order_status_name);

		if (GuaResult.getCode() == YesOrNoEnum.FAILD.code) {
			mailSendUtils.sendEmail("呱呱回调接口 超时取消 订单退款失败", "订单号：" + this.getMasterOrder().getOrderId());
			LOGGER.error("*****退款账单失败******* {}", JSON.toJSONString(TradeDto));
			return ResultWrapper.wrap(YesOrNoEnum.FAILD.code, YesOrNoEnum.FAILD.desc);
		}
		// 发送 超时取消已支付订单退款通知
		LOGGER.info("呱呱接口回调orderId={}:newState={},oladState={}", this.getMasterOrder().getOrderId(),
				this.getMasterOrder().getOrderStatus(), this.getOldStatus());
		// 超过15分钟 已支付 超时取消已支付订单退款 短信通知用户
		LOGGER.info("开始发送超时取消已支付订单退款 短信");
		// 发送派单短信
		LOGGER.info("准备开始发送超时取消已支付订单退款 短信》》》》》》》》》入参：mobile=" + this.getMasterOrder().getUserMobile() + ",address="
				+ this.getMasterOrder().getServiceAddress() + ",服务人员" + this.getMasterOrder().getServiceUserName()
				+ ",服务人员电话=" + this.getMasterOrder().getServiceUserPhone());
		messageSend.orderCancelSend(Long.parseLong(this.getMasterOrder().getUserMobile()),
				this.getMasterOrder().getServiceAddress());
		// 更新订单的支付状态
		return ResultWrapper.wrap(OrderStatusEnums.SUCCESS.getCode(), OrderStatusEnums.SUCCESS.getMessage());
	}

	/**
	 * 客户爽约
	 * @param orderStatusData
	 * @param carWashOrderDtos
     * @return
     */
	@Override
	public GuaResult customerMiss( OrderStatusData orderStatusData,
			List<CarWashOrderDto> carWashOrderDtos) {
		// 声明订单通知 对象
		GuaTradeDto guaTradeDto = new GuaTradeDto();
		GuaResult<GuaTradeDto> guaTradeDtoGuaResult = null;
		// 更新订单状态为完成,并添加取消原因
		for (CarWashOrderDto violationOrderDto : carWashOrderDtos) {
			violationOrderDto.setOrderStatus(DJOrderStatus.YIWANCHENG.code);
			violationOrderDto.setCancelReason("客户爽约");
		}
		// 修改订单状态不成功 直接返回消息
		if (!this.updateOrderSuper()) {
			LOGGER.info("订单同步失败 主订单为orderid{} ", this.getMasterOrder().getOrderId());
			return ResultWrapper.wrap(OrderStatusEnums.ERR_GETORDER.getCode(),
					OrderStatusEnums.ERR_GETORDER.getMessage());
		}
		// 发送esb消息
		try {
			LOGGER.info("客户爽约  发送esb消息  通知营销平台");
			GuaESBClient.sendEendGuaMes(this.getMasterOrder().getUid(),
					Long.parseLong(this.getMasterOrder().getUserMobile()), this.getMasterOrder().getOrderId(),
					Integer.parseInt(String.valueOf(this.getMasterOrder().getCityId())),
					this.getMasterOrder().getCreateTime(), new Date());
		} catch (Exception e) {
			LOGGER.error("客户爽约  发送esb消息 失败，原因：{}", e);
		}

		// 2.核销账单,全部
		List<Long> allSunOrderId = orderStatusData.getAllSunOrderId();
		allSunOrderId.add(this.getMasterOrder().getOrderId());
		guaTradeDto.setOrderIds(allSunOrderId);
		guaTradeDtoGuaResult = guaTradeService.finishBill(guaTradeDto);// 发送订单核销通知
		if (guaTradeDtoGuaResult == null || guaTradeDtoGuaResult.getCode() != 0) {
			mailSendUtils.sendEmail("呱呱回调接口 客户爽约 订单核销失败", "订单号：" + this.getMasterOrder().getOrderId());
			LOGGER.info("发送核销订单通知有误guaTradeDto：{}  errorMessage: {}", guaTradeDto.toString(),
					guaTradeDtoGuaResult.getMsg());
		}
		// 发送 用户爽约短信
		LOGGER.info("呱呱接口回调orderId={}:newState={},oladState={}", this.getMasterOrder().getOrderId(),
				this.getMasterOrder().getOrderStatus(), this.getOldStatus());
		// 用户爽约 发送订单取消短信 通知用户
		LOGGER.info("开始发送用户爽约 短信");
		// 发送派单短信
		LOGGER.info("准备开始发用户爽约 短信》》》》》》》》》入参：mobile=" + this.getMasterOrder().getUserMobile() + ",address="
				+ this.getMasterOrder().getServiceAddress() + ",服务人员" + this.getMasterOrder().getServiceUserName()
				+ ",服务人员电话=" + this.getMasterOrder().getServiceUserPhone());
		messageSend.cancelMiss(Long.parseLong(this.getMasterOrder().getUserMobile()),
				this.getMasterOrder().getServiceAddress());
		return ResultWrapper.wrap(OrderStatusEnums.SUCCESS.getCode(), OrderStatusEnums.SUCCESS.getMessage());
	}

	/**
	 * 协商撤单
	 * @param orderStatusData
	 * @param carWashOrderDtos
     * @return
     */
	@Override
	public GuaResult consultCancel(OrderStatusData orderStatusData,
			List<CarWashOrderDto> carWashOrderDtos) {
		// 声明订单通知 对象
		GuaTradeDto guaTradeDto = new GuaTradeDto();
		GuaResult<GuaTradeDto> guaTradeDtoGuaResult = null;
		// 更新订单状态为取消状态,并添加取消原因
		if (!this.cancelOrderSuper()) {
			LOGGER.info("主订单取消失败 主订单为orderid{} ", this.getMasterOrder().getOrderId());
			return ResultWrapper.wrap(OrderStatusEnums.ERR_ORDER_CANCEL.getCode(),
					OrderStatusEnums.ERR_ORDER_CANCEL.getMessage());
		}
		// 退款,全部
		List<Long> allSunOrderId = orderStatusData.getAllSunOrderId();
		allSunOrderId.add(this.getMasterOrder().getOrderId());
		guaTradeDto.setOrderIds(allSunOrderId);
		guaTradeDtoGuaResult = guaTradeService.refundBill(guaTradeDto);// 发送订单退款通知
		if (guaTradeDtoGuaResult == null || guaTradeDtoGuaResult.getCode() != 0) {
			mailSendUtils.sendEmail("呱呱回调接口 协商撤单 订单退款失败",
					"订单号：" + this.getMasterOrder().getOrderId() + "失败原因：" + guaTradeDtoGuaResult.getMsg());
			LOGGER.info("发送退款订单通知有误guaTradeDto：{}  errorMess age: {}", guaTradeDto.toString(),
					guaTradeDtoGuaResult.getMsg());
		}
		// 更新订单支付状态(已退款2)
		for (int i = 0; i < carWashOrderDtos.size(); i++) {
			carWashOrderDtos.get(i).setPayStatus(OrderPayStatusEnum.YITUIKUAN.code);
		}
		// 修改订单状态不成功 直接返回消息
		if (!this.updateOrderSuper()) {
			LOGGER.info("订单同步失败 主订单为orderid{} ", this.getMasterOrder().getOrderId());
			return ResultWrapper.wrap(OrderStatusEnums.ERR_GETORDER.getCode(),
					OrderStatusEnums.ERR_GETORDER.getMessage());
		}
		// 发送 协商退单短信
		LOGGER.info("呱呱接口回调orderId={}:newState={},oladState={}", this.getMasterOrder().getOrderId(),
				this.getMasterOrder().getOrderStatus(), this.getOldStatus());
		// 协商订单 发送订单取消短信 通知用户
		LOGGER.info("开始发送协商退单 短信");
		// 发送协商退单短信
		LOGGER.info("准备开始发协商退单 短信》》》》》》》》》入参：mobile=" + this.getMasterOrder().getUserMobile() + ",address="
				+ this.getMasterOrder().getServiceAddress() + ",服务人员" + this.getMasterOrder().getServiceUserName()
				+ ",服务人员电话=" + this.getMasterOrder().getServiceUserPhone());
		messageSend.cancelConsult(Long.parseLong(this.getMasterOrder().getUserMobile()),
				this.getMasterOrder().getServiceAddress());
		return ResultWrapper.wrap(OrderStatusEnums.SUCCESS.getCode(), OrderStatusEnums.SUCCESS.getMessage());
	}

	/**
	 * 客户取消
	 * @param
	 * @param orderStatusData
	 * @param carWashOrderDtos
     * @return
     */
	@Override
	public GuaResult customerCancel(OrderStatusData orderStatusData,
			List<CarWashOrderDto> carWashOrderDtos) {
		// 声明订单通知 对象
		GuaTradeDto guaTradeDto = new GuaTradeDto();
		GuaResult<GuaTradeDto> guaTradeDtoGuaResult = null;
		// 更新订单状态为取消状态,并添加取消原因
		if (!this.cancelOrderSuper()) {
			LOGGER.info("主订单取消失败 主订单为orderid{} ", this.getMasterOrder().getOrderId());
			return ResultWrapper.wrap(OrderStatusEnums.ERR_ORDER_CANCEL.getCode(),
					OrderStatusEnums.ERR_ORDER_CANCEL.getMessage());
		}
		// 已支付 进行退单
		if (orderStatusData.getOldStatus() >= 40 && orderStatusData.getOldStatus() < 70) {
			// 退款,全部
			List<Long> allSunOrderId = orderStatusData.getAllSunOrderId();
			allSunOrderId.add(this.getMasterOrder().getOrderId());
			guaTradeDto.setOrderIds(allSunOrderId);
			guaTradeDtoGuaResult = guaTradeService.refundBill(guaTradeDto);// 发送订单退款通知
			if (guaTradeDtoGuaResult == null || guaTradeDtoGuaResult.getCode() != 0) {
				mailSendUtils.sendEmail("呱呱回调接口 协商撤单 订单退款失败",
						"订单号：" + this.getMasterOrder().getOrderId() + "失败原因：" + guaTradeDtoGuaResult.getMsg());
				LOGGER.info("发送退款订单通知有误guaTradeDto：{}  errorMess age: {}", guaTradeDto.toString(),
						guaTradeDtoGuaResult.getMsg());
			}
			// 更新订单支付状态(已退款2)
			for (int i = 0; i < carWashOrderDtos.size(); i++) {
				carWashOrderDtos.get(i).setPayStatus(OrderPayStatusEnum.YITUIKUAN.code);
			}
			// 修改订单状态不成功 直接返回消息
			if (!this.updateOrderSuper()) {
				LOGGER.info("订单同步失败 主订单为orderid{} ", this.getMasterOrder().getOrderId());
				return ResultWrapper.wrap(OrderStatusEnums.ERR_GETORDER.getCode(),
						OrderStatusEnums.ERR_GETORDER.getMessage());
			}
			// 发送 协商退单短信
			LOGGER.info("呱呱接口回调orderId={}:newState={},oladState={}", this.getMasterOrder().getOrderId(),
					this.getMasterOrder().getOrderStatus(), this.getOldStatus());
			// 协商订单 发送订单取消短信 通知用户
			LOGGER.info("开始发送协商退单 短信");
			// 发送协商退单短信
			LOGGER.info("准备开始发协商退单 短信》》》》》》》》》入参：mobile=" + this.getMasterOrder().getUserMobile() + ",address="
					+ this.getMasterOrder().getServiceAddress() + ",服务人员" + this.getMasterOrder().getServiceUserName()
					+ ",服务人员电话=" + this.getMasterOrder().getServiceUserPhone());
			messageSend.cancelConsult(Long.parseLong(this.getMasterOrder().getUserMobile()),
					this.getMasterOrder().getServiceAddress());
			return ResultWrapper.wrap(OrderStatusEnums.SUCCESS.getCode(), OrderStatusEnums.SUCCESS.getMessage());
		}
		// 发送 客户取消短信
		LOGGER.info("呱呱接口回调orderId={}:newState={},oladState={}", this.getMasterOrder().getOrderId(),
				this.getMasterOrder().getOrderStatus(), this.getOldStatus());
		//  客户取消订单 发送订单取消短信 通知用户
		LOGGER.info("开始发送 客户取消 短信");
		// 发送协商退单短信
		LOGGER.info("准备开始发 客户取消 短信》》》》》》》》》入参：mobile=" + this.getMasterOrder().getUserMobile() + ",address="
				+ this.getMasterOrder().getServiceAddress() + ",服务人员" + this.getMasterOrder().getServiceUserName()
				+ ",服务人员电话=" + this.getMasterOrder().getServiceUserPhone());
		messageSend.cancelOrder(Long.parseLong(this.getMasterOrder().getUserMobile()),
				this.getMasterOrder().getServiceAddress());
		return ResultWrapper.wrap(OrderStatusEnums.SUCCESS.getCode(), OrderStatusEnums.SUCCESS.getMessage());
	}

	/**
	 * 默认调用父类更新订单方法
	 *
	 * @return
	 */
	@Override
	public boolean updateOrderSuper() {
		return super.updateOrder(this.getCarWashOrderDtos());
	}

	/**
	 * 默认调用父类取消订单方法
	 *在取消状态下需求右边 重写这个方法
	 * @return
	 */
	@Override
	public boolean cancelOrderSuper() {
		OrderDto orderDto = new OrderDto();
		orderDto.setMaster(true);
		orderDto.setOrderStatus(DJOrderStatus.YIQUXIAO.code);
		orderDto.setOrderId(this.getMasterOrder().getOrderId());
		// 取消原因
		if ( StringUtils.isNotBlank(this.getOrderStatusData().getOrderStatusName())) {
			orderDto.setCancelReason(this.getOrderStatusData().getOrderStatusName());
		}
		// 取消类型
		if(this.getOrderStatusData().getCancelReason()==20){
			orderDto.setCancelType(1);
		}else{
			orderDto.setCancelType(3);
		}
		// 取消时间
		orderDto.setCancelTime(new Date());
		return super.cancelOrder(orderDto);
	}

	public List<CarWashOrderDto> getCarWashOrderDtos() {
		return carWashOrderDtos;
	}

	public void setCarWashOrderDtos(List<CarWashOrderDto> carWashOrderDtos) {
		this.carWashOrderDtos = carWashOrderDtos;
	}

	public OrderDto getOrderDto() {
		return orderDto;
	}

	public void setOrderDto(OrderDto orderDto) {
		this.orderDto = orderDto;
	}

	public CarWashOrderDto getMasterOrder() {
		return masterOrder;
	}

	public void setMasterOrder(CarWashOrderDto masterOrder) {
		this.masterOrder = masterOrder;
	}

	public int getOldStatus() {
		return oldStatus;
	}

	public void setOldStatus(int oldStatus) {
		this.oldStatus = oldStatus;
	}

	private void updateStatusAndPayStatus(List<TcTradeBillDto> tcTradeBillDtos, Integer stauts,
			String order_status_name) {
		List<CarWashOrderDto> carWashOrderDtos = new ArrayList<CarWashOrderDto>();
		for (int i = 0; i < tcTradeBillDtos.size(); i++) {
			TcTradeBillDto tcTradeBillDto = tcTradeBillDtos.get(i);
			Long referId = tcTradeBillDto.getReferId();
			CarWashOrderDto carWashOrderDto = new CarWashOrderDto();
			if (referId.longValue() == tcTradeBillDto.getOrderId().longValue()) {
				carWashOrderDto.setMaster(true);
			}
			carWashOrderDto.setOrderId(tcTradeBillDto.getOrderId());
			carWashOrderDto.setOrderStatus(DJOrderStatus.YIQUXIAO.code);
			carWashOrderDto.setCancelType(3);
			carWashOrderDto.setCancelReason(order_status_name);
			if (stauts.intValue() == OrderPayStatusEnum.YIZHIFU.code) {
				carWashOrderDto.setPayStatus(OrderPayStatusEnum.YITUIKUAN.code);
				carWashOrderDto.setPayTime(new Date());
			}
			carWashOrderDtos.add(carWashOrderDto);
		}
		LOGGER.info("*******超时支付******* 更新订单库****** {}", JSON.toJSONString(carWashOrderDtos));
		carWashOrderAgent.updateOrder(carWashOrderDtos);
	}

	public OrderStatusData getOrderStatusData() {
		return orderStatusData;
	}

	public void setOrderStatusData(OrderStatusData orderStatusData) {
		this.orderStatusData = orderStatusData;
	}
}

</code><pre>
这个取消状态有点累赘，有想过从新管理一下（内嵌一个状态模式单独对这些取消状态从新管理），但是一时想不出比较好的办法。
