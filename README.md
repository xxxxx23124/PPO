重构造中，计划使用ppo训练自博弈下围棋。  
  
使用技术：mamba2，flash attention，cnn，SwiGLUMlp，torchrl，ppo，pettingZoo 
  
模型（时空分解注意力），mamba2处理时间步，flash attention处理空间（图像，每个像素点都当作一个token，其中可能加入cnn辅助注意力提取相邻的空间讯息）  
输入: (B, S, L, D)  
空间处理层 (Spatial Processor) Transformer的自注意力  
输入: (B*S, L, D)  
输出: (B*S, L, D)  
时间处理层 (Temporal Processor) Mamba2  
输入: (B*L, S, D)  
输出: (B*L, S, D)  
cnn辅助空间处理  
输入: (B, S, L->(H, W), D) -> (B*S, D, H, W)  
输出  (B*S, D, H, W)  
(B, S, L, D)->cnn辅助空间处理->空间处理层->时间处理层->...交替   
堆叠更多的交替层。通过深层堆叠，后一层的空间处理器可以间接地通过前一层的时间处理器获得时间信息，反之亦然。信息在层与层之间逐渐融合。  
  
todo：(窗口化/局部注意力、分层/金字塔结构、稀疏/低秩近似注意力) -> 可以处理更大的图片