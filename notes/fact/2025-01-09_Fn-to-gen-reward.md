---
date: 2025-01-09
type: fact
aliases:
  -
hubs:
  - "[[WASM]]"
---

# Fn to gen reward

In [[2025-01-06_Consume-reward-cell|this topic]], we implemented increasing the length of the snake body after the snake head eats a reward cell but reward cell won't disappear after the snake head eats it or either we need to generate a new reward cell after the snake head eats it.

We will implement a function to generate a new reward cell after the snake head eats it in this topic.

```rs
// src/lib.rs 

// ...

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize, size: usize) -> Self {
        let body: Vec<SnakeCell> = (0..size).map(|x| SnakeCell(spwan_idx + x)).collect();

        // these codes should be wrapped in a new function for reusability
        let mut reward_cell;
        loop {
            reward_cell = rnd(width * width);
            if !body.contains(&SnakeCell(reward_cell)) {
                break;
            }
        }

        World {
            width,
            snake: Snake {
                body,
                direction: Direction::Up,
            },
            next_cell: None,
            reward_cell,
        }
    }

    // ...

    pub fn step(&mut self) {
        let temp = self.snake.body.clone();
        match &self.next_cell {
            Some(cell) => {
                self.snake.body[0] = cell.clone();
                self.next_cell = None;
            }
            None => self.snake.body[0] = self.gen_next_snake_head(self.snake.direction),
        };
        (1..self.snake.body.len()).for_each(|i| self.snake.body[i].0 = temp[i - 1].0);

        if self.reward_cell == self.snake_head_idx() {
            self.snake.body.push(SnakeCell(self.snake.body[1].0));
            // because we need to reset the reward cell here
        }
    }
}
```

As explained in the annotation above, we need to encapsulate the codes that generate the reward cell into a function, and it should be a **static function** because we need to use it before the instance is created.

```rs

// ... 

#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize, size: usize) -> Self {
        let body: Vec<SnakeCell> = (0..size).map(|x| SnakeCell(spwan_idx + x)).collect();

        // extract the codes from here

        World {
            width,
            reward_cell: Self::gen_reward_cell(width, &body),
        //  ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^ 2) use function to generate reward cell and this line should be lifted up on top of `body` because of borrowing issue
            snake: Snake {
                body,  // body moved here, so reward_cell should be lifted up
                direction: Direction::Up,
            },
            next_cell: None,
        }
    }

    // 1) create a static function to wrap the codes that generate the reward
    fn gen_reward_cell(width: usize, body: &[SnakeCell]) -> usize {
        let mut reward_cell;
        loop {
            reward_cell = rnd(width * width);
            if !body.contains(&SnakeCell(reward_cell)) {
                break;
            }
        }

        reward_cell
    }

// ...

```

You can do a test to see if the app works as before.

Then, we need to implement the logic to generate a new reward cell after the snake head eats it.

```rs
#[wasm_bindgen]
impl World {
    pub fn new(width: usize, spwan_idx: usize, size: usize) -> Self {
        let body: Vec<SnakeCell> = (0..size).map(|x| SnakeCell(spwan_idx + x)).collect();

        World {
            width,
            reward_cell: Self::gen_reward_cell(width, &body),
            snake: Snake {
                body,
                direction: Direction::Up,
            },
            next_cell: None,
        }
    }

    fn gen_reward_cell(width: usize, body: &[SnakeCell]) -> usize {
        let mut reward_cell;
        loop {
            reward_cell = rnd(width * width);
            if !body.contains(&SnakeCell(reward_cell)) {
                break;
            }
        }

        reward_cell
    }

    // ...

    pub fn step(&mut self) {
        let temp = self.snake.body.clone();
        match &self.next_cell {
            Some(cell) => {
                self.snake.body[0] = cell.clone();
                self.next_cell = None;
            }
            None => self.snake.body[0] = self.gen_next_snake_head(self.snake.direction),
        };
        (1..self.snake.body.len()).for_each(|i| self.snake.body[i].0 = temp[i - 1].0);

        if self.reward_cell == self.snake_head_idx() {
            self.snake.body.push(SnakeCell(self.snake.body[1].0));
            self.reward_cell = Self::gen_reward_cell(self.width, &self.snake.body);   // generate new reward cell when the snake head eats it
        }
    }
}

```
```rs
