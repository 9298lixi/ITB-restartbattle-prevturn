# Into the Breach: prev / restart

[简体中文](#简体中文) · [English](#english)

## 简体中文

在游戏普通控制台中，输入 `prev` 回到上一个玩家回合，输入 `restart` 重开当前战斗。

### 兼容环境

- Windows Steam 版《Into the Breach》1.2.93。
- ModLoader 2.9.6。
- 当前原生 DLL 按该游戏构建适配，包含版本与函数签名检查。

### 安装

1. 先安装 [ITB ModLoader](https://github.com/itb-community/ITB-ModLoader/releases)。
2. 下载本仓库：点击 **Code → Download ZIP**，解压。
3. 把本仓库 `mods` 目录里的整个 `ITB_TurnRollback_Lua` 文件夹，放进游戏的 `mods` 目录。
4. 启动游戏，在 **Mod Content** 中启用 **Turn Rollback - prev / restart**，重启游戏。
5. 进入一场新的战斗。安装前已开始的战斗没有回合历史。

安装后的目录应为：

```text
Into the Breach/
└── mods/
    └── ITB_TurnRollback_Lua/
        └── scripts/
            ├── init.lua
            └── rollback_native.dll
```

升级时先把旧版本的这个 Mod 文件夹移到 `mods` 目录外，再放入新版本；同一 Mod ID 只保留一份。早期版本可能位于 `ITB_TurnRollback_Native_Attempt/ITB_TurnRollback_Lua`。

### 使用

在玩家回合、所有动画结束后，切换英文输入法，按键盘左上角的 <kbd>&#96;</kbd> / <kbd>~</kbd> 打开控制台。
使用普通命令模式，**不按 Tab，不加括号**。

| 命令 | 功能 |
| --- | --- |
| `prev` | 回到上一个玩家回合开头，撤销其后的敌方行动。可连续逐回合回退。 |
| `restart` | 恢复本场战斗最早的快照；通常为部署阶段，否则为首个已记录的玩家回合。 |

输入命令后按回车。第一回合没有更早的玩家回合时，`prev` 会提示没有可用快照。
重新加载当前存档时，可以继续使用随存档保留的本场历史。

### 快照清理

v1.1.0 在进入下一场新战斗时，自动清理当前玩家档案内的旧战斗快照，包括 v1.0 积累的快照。
当前战斗的快照保留；继续游戏和 `restart` 不触发清理。
清理只处理文件名和内容均确认属于本 Mod 的 `trXXXXXXXX.lua` 文件，不处理其他玩家档案和游戏普通存档。
同一场战斗的分支快照会保留到下一场战斗开始。清理失败会记录日志，下场战斗再重试。

### 排查问题

查看游戏目录的 `modloader.log`，搜索 `[TurnRollback]`：

- `Ready v1.1.0`：Mod 已加载。
- `snapshot`：已保存快照。
- `Board restored`：原生加载入口报告恢复成功。
- `Old snapshots cleaned`：已清理旧快照，`failed` 表示失败数量。
- `No history for this battle`：请进入一场新战斗。

### 源码与测试

- `mods/ITB_TurnRollback_Lua/scripts/init.lua`：命令逻辑、回合历史、旧快照清理。
- `src/rollback_native.c`：32 位 Windows 原生桥接源码。
- `mods/ITB_TurnRollback_Lua/scripts/rollback_native.dll`：预编译原生桥接，无需玩家自行编译。

通过游戏的原生保存/加载流程恢复棋盘；原生钩子仅作用于游戏进程，不修改游戏 EXE 或 ModLoader DLL 文件。

回退和重开已有游戏内验证。v1.1 自动清理已通过 Lua 5.1 逻辑及独立 32 位原生接口测试，仍需更多真实游戏验证。

### 卸载

退出游戏，把 `ITB_TurnRollback_Lua` 文件夹移出 `mods`，再重新打开游戏。

## English

Type `prev` in the game's regular console to return to the previous player turn, or `restart` to restart the current battle.

### Compatibility

- Into the Breach 1.2.93, Windows Steam version.
- ModLoader 2.9.6.
- The native DLL targets this game build and checks the executable version and function signatures before installing its hooks.

### Installation

1. Install [ITB ModLoader](https://github.com/itb-community/ITB-ModLoader/releases).
2. Download this repository using **Code → Download ZIP**, then extract it.
3. Copy the entire `ITB_TurnRollback_Lua` folder from this repository's `mods` directory into the game's `mods` directory.
4. Launch the game, enable **Turn Rollback - prev / restart** under **Mod Content**, then restart the game.
5. Enter a new battle. Battles already in progress when you install the mod have no recorded turn history.

The installed folder structure should be:

```text
Into the Breach/
└── mods/
    └── ITB_TurnRollback_Lua/
        └── scripts/
            ├── init.lua
            └── rollback_native.dll
```

When upgrading, move the old version of this mod outside the `mods` directory before installing the new version. Keep only one copy of this mod ID. Early versions may be located under `ITB_TurnRollback_Native_Attempt/ITB_TurnRollback_Lua`.

### Usage

During your turn, wait for all animations to finish, then press the <kbd>&#96;</kbd> / <kbd>~</kbd> key, usually below Esc, to open the console. Use an English keyboard layout or input method if needed.
Use the regular command mode: **do not press Tab or add parentheses**.

| Command | Effect |
| --- | --- |
| `prev` | Restore the beginning of the previous player turn, undoing the enemy actions that followed it. Repeat to go back through earlier recorded player turns. |
| `restart` | Restore the earliest snapshot of the current battle, usually deployment; otherwise, the first recorded player turn. |

Press Enter after typing a command. If there is no earlier recorded player turn, `prev` reports that no snapshot is available.
When you reload your current save, the battle history stored with that save remains available.

### Snapshot cleanup

Starting with v1.1.0, entering a new battle automatically removes old battle snapshots from the active player profile, including snapshots accumulated by v1.0.
Snapshots for the current battle are retained. Continuing a saved game and using `restart` do not trigger cleanup.
Cleanup only targets `trXXXXXXXX.lua` files whose names and contents identify them as snapshots created by this mod. Other player profiles and regular game saves are left untouched.
Snapshots from replayed branches of the same battle are retained until the next battle begins. Cleanup failures are logged and retried when another new battle starts.

### Troubleshooting

Open `modloader.log` in the game directory and search for `[TurnRollback]`:

- `Ready v1.1.0`: the mod has loaded.
- `snapshot`: a snapshot has been saved.
- `Board restored`: the native loader reported a successful restore.
- `Old snapshots cleaned`: old snapshots have been cleaned up; `failed` is the number of cleanup failures.
- `No history for this battle`: enter a new battle to start recording history.

### Source and testing

- `mods/ITB_TurnRollback_Lua/scripts/init.lua`: command handling, turn history, and old snapshot cleanup.
- `src/rollback_native.c`: source for the 32-bit Windows native bridge.
- `mods/ITB_TurnRollback_Lua/scripts/rollback_native.dll`: the precompiled native bridge; players do not need to compile it.

The mod restores the board through the game's native save/load flow. Its hooks operate within the running game process and do not modify the game executable or ModLoader DLL files on disk.

Turn rollback and battle restart have been tested in the game. The automatic cleanup added in v1.1 has passed Lua 5.1 logic tests and standalone 32-bit native interface tests; it still needs broader in-game testing.

### Uninstalling

Exit the game, move `ITB_TurnRollback_Lua` outside the `mods` directory, then launch the game again.
