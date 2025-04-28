---
date: 2025-03-18
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Body types

```lua
-- Love2D 物理引擎中的碰撞體類型 (Body Types)

-- 1. Static (靜態)
-- - 完全不會移動
-- - 具有無限質量
-- - 不受任何力、衝量或碰撞的影響
-- - 可以與所有類型的物體發生碰撞
-- - 適用於：關卡邊界、地面、牆壁等固定不動的物體
local staticBody = love.physics.newBody(world, x, y, "static")

-- 2. Kinematic (運動學)
-- - 可以被程式控制移動，但不會受到物理引擎的力和衝量影響
-- - 只會與 dynamic 物體發生碰撞，不會與其他 static 或 kinematic 物體碰撞
-- - 不會受到重力影響
-- - 適用於：移動平台、電梯、門等需要移動但不受物理影響的物體
local kinematicBody = love.physics.newBody(world, x, y, "kinematic")
-- 移動 kinematic 物體的方法
kinematicBody:setLinearVelocity(vx, vy)  -- 設置線性速度
kinematicBody:setPosition(x, y)  -- 直接設置位置

-- 3. Dynamic (動態)
-- - 會與所有類型的物體碰撞
-- - 受到物理引擎中的力、衝量和重力的影響
-- - 適用於：玩家角色、敵人、投射物等需要真實物理行為的物體
local dynamicBody = love.physics.newBody(world, x, y, "dynamic")
-- 對 dynamic 物體施加力和衝量
dynamicBody:applyForce(fx, fy)  -- 施加力
dynamicBody:applyLinearImpulse(ix, iy)  -- 施加線性衝量
```

