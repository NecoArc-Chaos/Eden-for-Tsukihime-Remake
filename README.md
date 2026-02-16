# Eden-for-Tsukihime-Remake

通过Aphcity大佬提供的方法 (https://b23.tv/jXFjBj7) 建立了自动构建，适用于**无贴图错误**游玩月姬R  
*可能不适用于玩TotK*  

**文件获取：**  
找到上面的Action  
进入并点进最新构建  
在下面有自动构建产物  

本人小白，全程使用Gemini 3  
计划构建安卓arm64-v8a版本和windows x86-64版本
其他需求自己去构建吧～  

**Windows端**也建议使用我狐哥打包的
不用手动安装密钥固件和游戏
进月R贴吧群获取→677426945

参考文档: 
https://git.eden-emu.dev/eden-emu/eden/src/branch/master/docs/Build.md

意义:   
- 性能更高，Mali支持更强  
- 修复了月姬R等某些2D游戏的贴图错误  

---

**目前的构建产物还不能实现无贴图错误，请耐心等待……**  

---

注意版本号可能并不匹配实际版本  
反正构建都是拉取上游最新的更新  


不要点击应用内提示的更新新版本！  


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