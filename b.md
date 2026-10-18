flowchart TB
    %% ==================== 业务使用端 ====================
    subgraph A["一、业务使用端"]
        direction LR
        POS["农行 POS 端应用<br/>商品选购 / 金额确认<br/>扫码支付 / 小票打印"]
        APP["商户商城 手机/平板 APP<br/>商品展示 / 下单 / 订单查询<br/>付款码展示"]
        OPS["运营管理端<br/>商户及门店账号开通<br/>权限管理 / 运营配置"]
    end

    %% ==================== 业务服务层 ====================
    subgraph B["二、农行国补交易综合服务系统"]
        direction TB

        AUTH["身份认证与权限管理"]

        subgraph B1["核心业务服务"]
            direction LR
            MER["商品与商户管理<br/>商品信息 / 优惠码<br/>商户及门店信息"]
            ORD["订单管理与交易关联<br/>订单创建 / 状态跟踪"]
            PAY["支付请求与结果处理<br/>支付金额 / 交易结果"]
            SUB["国补核销协同服务<br/>券信息 / 核销请求 / 核销结果"]
        end

        subgraph B2["运营及数据管理"]
            direction LR
            REC["对账文件管理<br/>文件生成 / 上传 / 回盘解析"]
            EXC["异常处理与差异核查<br/>匹配失败 / 信息补录"]
            LOG["日志 / 监控 / 审计<br/>异常告警 / 操作追溯"]
        end
    end

    %% ==================== 外部系统 ====================
    subgraph C["三、外部系统与业务平台"]
        direction LR
        ABC["农行支付及收单系统<br/>支付受理 / 支付结果"]
        UNION["银联国补核销及营销平台<br/>资格校验 / 券核销<br/>营销匹配 / 交易结果"]
        FILE["银联文件交换平台<br/>收单流水文件上送<br/>匹配结果回盘"]
    end

    %% ==================== 访问关系 ====================
    POS --> AUTH
    APP --> AUTH
    OPS --> AUTH

    AUTH --> MER
    AUTH --> ORD

    MER --> ORD
    ORD --> PAY
    ORD --> SUB

    %% 支付及国补协同
    PAY <--> ABC
    SUB <--> UNION

    %% 交易结果与对账
    ABC --> PAY
    PAY --> REC
    SUB --> REC
    REC <--> FILE
    FILE --> EXC
    REC --> EXC

    %% 管理与审计
    OPS --> MER
    OPS --> REC
    MER -.-> LOG
    ORD -.-> LOG
    PAY -.-> LOG
    SUB -.-> LOG
    REC -.-> LOG
    EXC -.-> LOG

    %% ==================== 样式 ====================
    classDef terminal fill:#E8F1FC,stroke:#3973AC,color:#163A5F,stroke-width:1.5px
    classDef service fill:#E8F5F0,stroke:#39856A,color:#174D3B,stroke-width:1.5px
    classDef external fill:#FFF3DF,stroke:#C58A2B,color:#704A10,stroke-width:1.5px
    classDef manage fill:#F0EAFE,stroke:#8064B1,color:#39265F,stroke-width:1.5px

    class POS,APP,OPS terminal
    class AUTH,MER,ORD,PAY,SUB service
    class REC,EXC,LOG manage
    class ABC,UNION,FILE external
