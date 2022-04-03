# video convert
## 兩種影像編碼方式
由於 cv2 在讀某些檔案會偵測不到正確 FPS，因此可以使用 imageio 解決。
### 方法一: 採用 cv2 讀取影像
- 編碼方式: MPEG-4 Video
- mp4 檔

```py
%%time
import time
import cv2
from PIL import Image
import numpy as np

ARGS_VIDEO='./data/demo.mp4' # 'path to input image'
ARGS_OUTPUT_FORMAT='mp4v'
ARGS_OUTPUT='output.mp4' # 'path to output image'
ARGS_PREVIEW=False

video_path = ARGS_VIDEO

vid = cv2.VideoCapture(video_path)

if ARGS_OUTPUT:
    # by default VideoCapture returns float instead of int
    width = int(vid.get(cv2.CAP_PROP_FRAME_WIDTH))
    height = int(vid.get(cv2.CAP_PROP_FRAME_HEIGHT))
    fps = int(vid.get(cv2.CAP_PROP_FPS))
    codec = cv2.VideoWriter_fourcc(*ARGS_OUTPUT_FORMAT)
    out = cv2.VideoWriter(ARGS_OUTPUT, codec, fps, (width, height))
    print(ARGS_OUTPUT, codec, fps, (width, height))

frame_id = 0
while True:
    return_value, frame = vid.read()
    if return_value:
        image = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
    else:
        print("Video processing complete")
        break
    result = cv2.cvtColor(image, cv2.COLOR_RGB2BGR)

    if ARGS_OUTPUT:
        result = cv2.resize(result, (width, height), interpolation=cv2.INTER_AREA) # resize image
        result = np.asarray(result)
        out.write(result)
        
    frame_id += 1
out.release() 
```

### 方法二: 採用 imageio 讀取影像
- 編碼方式: H.264
- mp4 檔

```py
%%time
import imageio
# load video
reader = imageio.get_reader('./data/demo.mp4')
# get video fps
fps = reader.get_meta_data()['fps']
writer = imageio.get_writer('./output.mp4', fps=fps)

print(f'video info: {reader.get_meta_data()}')
for img in reader:
    writer.append_data(img)
writer.close()
```