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


