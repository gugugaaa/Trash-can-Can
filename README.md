# Trash-can-Can

安徽省工创赛垃圾分类赛道,视觉和电控部分代码，支持在PC和树莓派平台上运行。

## Features

- 利用YOLO11进行实时垃圾分类
- 跨平台支持（PC和树莓派）
- Arduino集成，用于硬件控制
- 视频录制功能
- 适用于不同场景的多个预训练模型

## Quick Start

### 环境要求

- 可在CPU上轻松运行（PC版）
- Raspberry Pi 4（树莓派版）
- Arduino板（可选）

### 使用说明

#### PC版
```bash
python detect_pc.py [--model models/trashcan.pt] [--source 0]
```

#### 树莓派版
```bash
python detect_pi.py [--model models/trashcan.pt] [--source 0]
```

#### 录制检测结果
```bash
python detect_record.py [--output output.mp4]
```

## Project Structure

```
.
├── detect_pc.py      # PC端检测脚本
├── detect_pi.py      # 树莓派检测脚本
├── detect_record.py  # 视频录制脚本
├── models/           # 预训练模型
├── images/           # 测试图像
│   ├── origin_img/   # 原始图像
│   ├── json_labels/  # Labelme JSON标签
│   └── yolo_labels/  # YOLO格式标签
├── datasets/         # 训练数据集
│   ├── train/        # 训练集
│   └── valid/        # 验证集
└── process_data/     # 处理自定义数据的工具
    └── box/          # 边界框转换工具
```

## Data Processing Guide

我准备了一套数据处理管道:

1. **准备原始图像**
   - 将原始图像放入 `images/origin_img/`

2. **标注图像**
   - 使用 Labelme 或 Label-studio 进行标注
   - 从 `images/origin_img/` 打开图像
   - 保存 JSON 标注文件

3. **清理未标注图像**
   ```bash
   python clear_not_labeled_img.py
   ```
   该命令会删除未标注图像对应的 JSON 文件

4. **整理标签文件**
   - 将所有 JSON 标签文件移动到 `images/json_labels/`

5. **转换为 YOLO 格式**
   ```bash
   python process_data/box/labelme2yolo.py
   ```
   这会将 JSON 标签转换为 YOLO 格式，并保存到 `images/yolo_labels/`

6. **准备训练数据**
   - 将 `images/origin_img/` 和 `images/yolo_labels/` 中的所有文件复制到 `datasets/train/`

7. **划分数据集**
8. **数据增强**
   - 建议在训练时划分和增强, yolo默认的增强即可

9. **打包用于训练**
   - 确保 `mydata_kaggle.yaml` 配置正确
   - 使用 Bandizip 压缩数据集 (win11自带的会多一层目录, 导致混乱)
   - 上传到 Kaggle 进行训练

## Models

| 模型名称 | 尺寸 | 描述 | 最适合 |
|------------|------|-------------|----------|
| trashcan.pt | 320x320 | 包含蔬菜类别 | 通用 |
| trashcan_640.pt | 640x640 | 不含蔬菜类别 | 高准确率 |

## Dataset

数据集组织如下：

```
datasets/
├── train/
├── valid/
└── mydata_kaggle.yaml
```

配置文件：
- `mydata_kaggle.yaml`：Kaggle 训练配置

![数据集可视化](https://i.ibb.co/RTpCwkQB/dataset-visualize.png)

## Development

我遇到这些问题:
- 模型在不同光照条件下的鲁棒性不足。
- 把碎瓷片和白萝卜条区分开


## License

本项目基于 Apache License 2.0 许可 - 详情请见 [LICENSE](LICENSE) 文件。

## 致谢

- [Ultralytics](https://github.com/ultralytics/ultralytics) 提供 YOLO
- [Kaggle](https://www.kaggle.com) 提供 GPU 资源
- 特别感谢硬件实现团队
