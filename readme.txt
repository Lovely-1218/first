### 危险检测流程图

```mermaid
flowchart TD
    %% 样式定义
    classDef startEnd fill:#E8F4FD,stroke:#1E50B3,stroke-width:1px,color:#000;
    classDef io fill:#F5F5F5,stroke:#666666,stroke-width:1px,color:#000;
    classDef process fill:#E6F7E9,stroke:#237D31,stroke-width:1px,color:#000;
    classDef decision fill:#FFF3E0,stroke:#D86000,stroke-width:1px,color:#000;
    classDef highRisk fill:#FFEBEE,stroke:#D32F2F,stroke-width:1px,color:#000;

    %% 流程节点
    A([开始]):::startEnd --> B[/输入YOLO检测结果与同步深度图/]:::io
    B --> C[过滤class_id≥4的危险类目标]:::process
    C --> D[目标三维坐标解算\n深度提取→中值滤波→坐标转换]:::process
    D --> E[DeepSORT跨帧跟踪\n特征提取→轨迹匹配→ID维护]:::process
    E --> F[3D卡尔曼滤波\n状态建模→位置/速度平滑预测]:::process
    F --> G[自运动补偿→相对距离/径向速度/TTC计算]:::process
    G --> H{TTC危险等级判定}:::decision
    H -->|TTC>5s 或 径向速度vr≥0| I[安全，无需预警]:::process
    H -->|3s<TTC≤5s| J[低风险，语音播报提示]:::process
    H -->|1.5s<TTC≤3s| K[中风险，双手环单次震动]:::process
    H -->|TTC≤1.5s| L[高风险，双手环持续震动]:::highRisk
    I --> M[输出目标信息与预警结果]:::process
    J --> M
    K --> M
    L --> M
    M --> N([结束]):::startEnd
