# Mongoose

Mongoose는 관계형 데이터베이스가 아닌 문서형 데이터베이스로 문서 관점으로 실행되는 ODM (Object-Document Mapping Library)입니다.\
또한 모델을 정의해서 모든 쿼리가 배후에서 작성되도록 돕기 때문에 쿼리 대신 데이터를 다룰 수 있도록 만듭니다.

```bash
npm i mongoose
```

## 주요 기능

1. 데이터 모델의 구조를 정의할 수 있는 스키마(Schema) 기능을 제공합니다.
2. 데이터가 데이터베이스에 저장되기 전에 유효성을 검사할 수 있는 기능을 제공합니다.
3. 복잡한 쿼리를 쉽게 작성하고 실행할 수 있는 메서드를 제공합니다.
4. 데이터베이스 작업 전후에 특정 작업을 수행할 수 있는 미들웨어 기능을 제공하여 데이터가 저장되기 전에 자동으로 값을 설정하거나, 삭제되기 전에 특정 검사를 수행할 수 있습니다.

### 몽고디비 연결

```tsx
const mongoose = require('mongoose');

const connect = () => {
// 개발 환경일 때만 콘솔을 통해 몽구스가 생성하는 쿼리 내용을 확인할 수 있게 하는 코드
  if (process.env.NODE_ENV !== 'production') {
    mongoose.set('debug', true);
  }

// 몽구스와 몽고디비를 연결하는 부분
  mongoose.connect('mongodb://root:nodejsbook@localhost:27017/admin', {
    dbName: 'nodejs',
    useNewUrlParser: true,
  }, (error) => {
    if (error) {
      console.log('몽고디비 연결 에러', error);
    } else {
      console.log('몽고디비 연결 성공');
    }
  });
};


// 에러 발생 시 에러 내용을 기록하고, 연결 종료 시 재연결을 시도
mongoose.connection.on('error', (error) => {
  console.error('몽고디비 연결 에러', error);
});
mongoose.connection.on('disconnected', () => {
  console.error('몽고디비 연결이 끊겼습니다. 연결을 재시도합니다.');
  connect();
});
```

### 스키마(Schema)

스키마는 데이터베이스에 저장될 문서(document)의 구조를 정의하는 청사진입니다.\
스키마를 통해 각 문서가 어떤 필드를 가지며, 그 필드들이 어떤 타입의 데이터를 저장하는지를 미리 정할 수 있습니다.\
몽고디비에 데이터를 넣기 전에 노드 서버 단에서 데이터를 한 번 필터링하는 역할을 합니다.

#### 스키마의 예시

몽구스는 알아서 `_id`를 기본 키로 생성하므로 `_id` 필드는 적어줄 필요가 없습니다.\
나머지 필드의 스펙만 입력합니다.

유저 정보를 저장하는 데이터베이스를 만들고 싶다면, 유저의 이름, 결혼정보, 기타 코멘트 등의 정보가 필요할 수 있습니다.\
이를 스키마로 정의하면 다음과 같습니다:

```javascript
const mongoose = require('mongoose');

const { Schema } = mongoose;

const userSchema = new Schema({
  name: {
    type: String,
    required: true,
    unique: true,
  },
  age: {
    type: Number,
    required: true,
  },
  married: {
    type: Boolean,
    required: true,
  },
  comment: String,
  createdAt: {
    type: Date,
    default: Date.now,
  },
});

module.exports = mongoose.model('User', userSchema);
```

몽구스 스키마는 `String`, `Number`, `Date`, `Buffer`, `Boolean`, `Mixed`, `ObjectId`, `Array`를 값으로 가질 수 있습니다.

댓글 스키마를 만들어 봤습니다.

```tsx
const mongoose = require('mongoose');

const { Schema } = mongoose;
const { Types: { ObjectId } } = Schema;
const commentSchema = new Schema({
  commenter: {
    type: ObjectId,
    required: true,
    ref: 'User',
  },
  comment: {
    type: String,
    required: true,
  },
  createdAt: {
    type: Date,
    default: Date.now,
  },
});

// comments 컬렉션 생성
module.exports = mongoose.model('Comment', commentSchema);
```

`commenter`필드의 `type`은 `ObjectId`이며, 옵션으로 `ref` 속성의 값이 `User`로 주어져 있습니다.\
`commenter` 필드에 `User` 스키마의 사용자 `ObjectId`가 들어간다는 뜻입니다.

몽구스는 `model` 메서드의 첫 번째 인수로 컬렉션 이름을 만듭니다.\
첫 번째 인수가 `User`라면 첫 글자를 소문자로 만든 뒤 복수형으로 바꿔서 `users` 컬렉션을 생성합니다.

#### 청사진(blueprint)

건축에 사용되는 설계도로 어떤 것을 만들기 위해 계획하고 설계할 때 그 기본 구조나 계획을 뜻하는 비유적인 표현으로도 사용됩니다.
위의 경우 데이터베이스에 저장될 데이터의 구조와 형태를 미리 정의하는 계획서로 사용됩니다.

### 모델(Model)

스키마를 기반으로 만들어진 데이터베이스 조작 도구입니다.\
모델을 통해 실제 데이터베이스와 상호작용하여 데이터를 생성, 읽기, 수정, 삭제(CRUD)할 수 있습니다.

#### 모델의 예시

스키마를 정의한 후, 이를 `model`로 변환하여 사용합니다.

```javascript
const Student = mongoose.model('Student', studentSchema);
```

> `mongoose.model('model 이름', schema)`의 model 이름이 mongoDB의 `collection`이 됩니다.

`Student` 모델로 학생 정보 조회

```javascript
Student.find({ grade: 'A' }, (err, students) => {
  if (err) return console.error(err);
  console.log(students);
});
```

### 쿼리(Query)

데이터베이스에서 원하는 데이터를 찾거나 조작하기 위해 사용하는 명령어입니다.\
쿼리를 통해 특정 정보를 가져오거나 수정할 수 있습니다.\
MongoDB와 메서드가 다르므로 Mongoose용 메서드를 따로 외워야 합니다.

#### 쿼리 예시

`find({})` 메서드로 모든 사용자를 찾은 뒤, `users` 변수로 넣을 수 있습니다.\
`find` 메서드는 `User` 스키마를 `require`한 뒤 사용할 수 있습니다.\
몽고디비의 `db.users.find({})` 쿼리와 같습니다.

```tsx
// index.js

const express = require('express');
const User = require('../schemas/user');

const router = express.Router();

router.get('/', async (req, res, next) => {
  try {
    const users = await User.find({});
    res.render('mongoose', { users });
  } catch (err) {
    console.error(err);
    next(err);
  }
});

module.exports = router;
```

사용자를 등록할 때는 먼저 모델 `create` 메서드로 저장합니다.

댓글을 쓴 사용자의 아이디로 댓글을 조회한 뒤 `populate` 메서드로 관련 있는 컬렉션의 다큐먼트를 불러올 수 있습니다.\
위에 나온 스키마(Schema)설명에서 `Comment` 스키마 `commenter` 필드의 `ref`가 `User`로 되어 있으므로, 자동으로 `users` 컬렉션에서 사용자 다큐먼트를 찾아 합칩니다.\
`commenter` 필드는 `ObjectId`가 아니라 그 `ObjectId`를 가진 사용자 다큐먼트가 됩니다.

```tsx
// user.js

const express = require('express');
const User = require('../schemas/user');
const Comment = require('../schemas/comment');

const router = express.Router();

router.route('/')
  // 사용자를 조회
  .get(async (req, res, next) => {
    try {
      const users = await User.find({});
      res.json(users);
    } catch (err) {
      console.error(err);
      next(err);
    }
  })
  // 사용자를 등록
  .post(async (req, res, next) => {
    try {
      const user = await User.create({
        name: req.body.name,
        age: req.body.age,
        married: req.body.married,
      });
      console.log(user);
      res.status(201).json(user);
    } catch (err) {
      console.error(err);
      next(err);
    }
  });

// 댓글 다큐먼트를 조회
router.get('/:id/comments', async (req, res, next) => {
  try {
    const comments = await Comment.find({ commenter: req.params.id })
      .populate('commenter');
    console.log(comments);
    res.json(comments);
  } catch (err) {
    console.error(err);
    next(err);
  }
});

module.exports = router;
```

`Comment.create` 메서드로 댓글을 저장합니다.\
`populate` 메서드로 프로미스의 결과로 반환된 `comment` 객체에 다른 컬렉션 다큐먼트를 불러옵니다.\
`path` 옵션으로 어떤 필드를 합칠지 설정할 수 있습니다.

`update` 메서드의 첫 번째 인수로는 어떤 다큐먼트를 수정할지를 나타낸 쿼리 객체를 제공하고, 두 번째 인수로는 수정할 필드와 값이 들어 있는 객체를 제공합니다.

`remove` 메서드는 어떤 다큐먼트를 삭제할지에 대한 조건을 첫 번째 인수에 넣습니다.

```tsx
// comments.js

const express = require('express');
const Comment = require('../schemas/comment');

const router = express.Router();

// 다큐먼트를 등록
router.post('/', async (req, res, next) => {
  try {
    const comment = await Comment.create({
      commenter: req.body.id,
      comment: req.body.comment,
    });
    console.log(comment);
    const result = await Comment.populate(comment, { path: 'commenter' });
    res.status(201).json(result);
  } catch (err) {
    console.error(err);
    next(err);
  }
});

// 다큐먼트를 수정
router.route('/:id')
  .patch(async (req, res, next) => {
    try {
      // _id가 req.params.id인 다큐먼트의 comment를 req.body.comment로 수정
      const result = await Comment.update({
        _id: req.params.id,
      }, {
        comment: req.body.comment,
      });
      res.json(result);
    } catch (err) {
      console.error(err);
      next(err);
    }
  })
  //  다큐먼트를 삭제
  .delete(async (req, res, next) => {
    try {
      const result = await Comment.remove({ _id: req.params.id });
      res.json(result);
    } catch (err) {
      console.error(err);
      next(err);
    }
  });

module.exports = router;
```

## 참고 자료

- [Node.js 교과서 개정 3판](https://thebook.io/080334/)
