# Eden-for-Tsukihime-Remake

通过Aphcity大佬提供的方法 (https://b23.tv/jXFjBj7) 建立了自动构建，适用于**无贴图错误**游玩月姬R  
*可能不适用于玩TotK*  

本人小白，全程使用Gemini 3  
计划构建安卓arm64-v8a版本和windows x86-64版本，应该就够了吧，其他需求自己去构建吧～

目前的构建产物还不能实现无贴图错误，请耐心等待……

注意版本号可能并不匹配实际版本  
反正构建都是拉取上游最新的更新  

## 文件修改参考：  

- src/video_core/texture_cache/util.cpp  
把大约在126行的代码  

```cpp
.depth = level == 0 && num_levels == 1
                     ? block_size.depth
                     : AdjustMipBlockSize<GOB_SIZE_Z>(num_tiles.depth, block_size.depth, level),
```

改为  

```cpp
.depth = AdjustMipBlockSize<GOB_SIZE_Z>(num_tiles.depth, block_size.depth, level),
```

删除171-177行（经过上一步修改）  
上一行是  

```cpp
[[nodiscard]] constexpr Extent3D TileShift(const LevelInfo& info, u32 level) {
//删掉以下代码，上面别删
    if (level == 0 && info.num_levels == 1) {
        return Extent3D{
            .width = info.block.width,
            .height = info.block.height,
            .depth = info.block.depth,
        };
    }
//注意别删多或删少了
```

将1310~1312行  

```cpp
static_assert(CalculateLevelSize(LevelInfo{{32, 32, 1}, {0, 0, 4}, {1, 1}, 4, 0, 1}, 0) == 0x40000);

static_assert(CalculateLevelSize(LevelInfo{{128, 8, 1}, {0, 4, 0}, {1, 1}, 4, 0, 1}, 0) == 0x40000);
```

改为  

```cpp
static_assert(CalculateLevelSize(LevelInfo{{32, 32, 1}, {0, 0, 4}, {1, 1}, 4, 0}, 0) == 0x4000);
```