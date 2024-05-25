# Mongoose

Mongoose는 관계형 데이터베이스가 아닌 문서형 데이터베이스로 문서 관점으로 실행되는 ODM (Object-Document Mapping Library)입니다.\
또한 모델을 정의해서 모든 쿼리가 배후에서 작성되도록 돕기 때문에 쿼리 대신 데이터를 다룰 수 있도록 만듭니다.

## 주요 기능

1. 데이터 모델의 구조를 정의할 수 있는 스키마(Schema) 기능을 제공합니다.
2. 데이터가 데이터베이스에 저장되기 전에 유효성을 검사할 수 있는 기능을 제공합니다.
3. 복잡한 쿼리를 쉽게 작성하고 실행할 수 있는 메서드를 제공합니다.
4. 데이터베이스 작업 전후에 특정 작업을 수행할 수 있는 미들웨어 기능을 제공하여 데이터가 저장되기 전에 자동으로 값을 설정하거나, 삭제되기 전에 특정 검사를 수행할 수 있습니다.

### 스키마(Schema)

스키마는 데이터베이스에 저장될 문서(document)의 구조를 정의하는 청사진입니다.\
스키마를 통해 각 문서가 어떤 필드를 가지며, 그 필드들이 어떤 타입의 데이터를 저장하는지를 미리 정할 수 있습니다.

#### 스키마의 예시

학생 정보를 저장하는 데이터베이스를 만들고 싶다면, 학생의 이름, 나이, 학년 등의 정보가 필요할 수 있습니다.\
이를 스키마로 정의하면 다음과 같습니다:

```javascript
const mongoose = require('mongoose');
const Schema = mongoose.Schema;

const studentSchema = new Schema({
  name: { type: String, required: true },
  age: { type: Number, required: true },
  grade: { type: String, required: true }
});
```

#### 청사진(blueprint)

건축에 사용되는 설계도로 어떤 것을 만들기 위해 계획하고 설계할 때 그 기본 구조나 계획을 뜻하는 비유적인 표현으로도 사용됩니다.
위의 경우 데이터베이스에 저장될 데이터의 구조와 형태를 미리 정의하는 계획서로 사용됩니다.

### 모델(Model)

스키마를 기반으로 만들어진 데이터베이스 조작 도구입니다.\
모델을 통해 실제 데이터베이스와 상호작용하여 데이터를 생성, 읽기, 수정, 삭제(CRUD)할 수 있습니다.

#### 모델의 예시

스키마를 정의한 후, 이를 model로 변환하여 사용합니다.

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
쿼리를 통해 특정 정보를 가져오거나 수정할 수 있습니다.
