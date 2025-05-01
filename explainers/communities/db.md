## БД сообществ с учетом пользователей и модерации

```javascript
// Коллекции для основных сущностей

// 1. Communities (Сообщества)
{
  _id: ObjectId,
  name: String,
  description: String,
  avatar: String,
  cover: String,
  createdAt: Date,
  createdBy: String, // address hash
  settings: {
    isPrivate: Boolean,
    joinPolicy: String, // "open", "moderated", "invite"
    contentPolicy: String, // "moderated", "free"
    languages: [String]
  },
  moderators: [{
    address: String,
    role: String, // "owner", "admin", "moderator"
    addedAt: Date,
    addedBy: String
  }],
  stats: {
    membersCount: Number,
    postsCount: Number,
    activeMembers: Number
  }
}

// 2. CommunityMembers (Участники сообществ)
{
  _id: ObjectId,
  communityId: ObjectId,
  memberAddress: String,
  joinedAt: Date,
  status: String, // "active", "banned", "left"
  role: String, // "member", "moderator", "admin"
  stats: {
    postsCount: Number,
    commentsCount: Number,
    reputation: Number
  }
}

// 3. CommunityContent (Контент сообщества)
{
  _id: ObjectId,
  communityId: ObjectId,
  type: String, // на основе TxType (CONTENT_POST, CONTENT_VIDEO, etc.)
  txId: String,
  authorAddress: String,
  createdAt: Date,
  content: {
    title: String,
    body: String,
    media: [{
      type: String,
      url: String
    }],
    tags: [String]
  },
  moderation: {
    status: String, // "pending", "approved", "rejected"
    moderatedBy: String,
    moderatedAt: Date,
    reason: String
  },
  stats: {
    views: Number,
    likes: Number,
    comments: Number
  }
}

// 4. CommunityComments (Комментарии)
{
  _id: ObjectId,
  communityId: ObjectId,
  contentId: ObjectId,
  txId: String,
  parentId: ObjectId, // для древовидных комментариев
  authorAddress: String,
  createdAt: Date,
  content: String,
  moderation: {
    status: String,
    moderatedBy: String,
    moderatedAt: Date
  },
  stats: {
    likes: Number,
    replies: Number
  }
}

// 5. CommunityActions (Действия в сообществе)
{
  _id: ObjectId,
  communityId: ObjectId,
  type: String, // на основе TxType (ACTION_SCORE_CONTENT, ACTION_SCORE_COMMENT, etc.)
  txId: String,
  targetId: ObjectId, // ID контента или комментария
  authorAddress: String,
  createdAt: Date,
  value: Number, // для оценок
  data: Mixed // дополнительные данные в зависимости от типа действия
}

// 6. CommunityModeration (Модерация)
{
  _id: ObjectId,
  communityId: ObjectId,
  type: String, // "content", "comment", "user"
  targetId: ObjectId,
  status: String, // "pending", "approved", "rejected"
  moderatorAddress: String,
  createdAt: Date,
  resolvedAt: Date,
  reason: String,
  verdict: {
    decision: String,
    explanation: String,
    votesFor: Number,
    votesAgainst: Number
  }
}

// 7. CommunityInvites (Приглашения)
{
  _id: ObjectId,
  communityId: ObjectId,
  inviterAddress: String,
  inviteeAddress: String,
  status: String, // "pending", "accepted", "rejected"
  createdAt: Date,
  resolvedAt: Date,
  code: String
}
```


```javascript
db.Communities.createIndex({ "name": 1 }, { unique: true });
db.Communities.createIndex({ "createdBy": 1 });

db.CommunityMembers.createIndex({ "communityId": 1, "memberAddress": 1 }, { unique: true });
db.CommunityMembers.createIndex({ "memberAddress": 1 });

db.CommunityContent.createIndex({ "communityId": 1, "createdAt": -1 });
db.CommunityContent.createIndex({ "txId": 1 }, { unique: true });
db.CommunityContent.createIndex({ "authorAddress": 1 });
db.CommunityContent.createIndex({ "tags": 1 });

db.CommunityComments.createIndex({ "communityId": 1, "contentId": 1 });
db.CommunityComments.createIndex({ "txId": 1 }, { unique: true });
db.CommunityComments.createIndex({ "authorAddress": 1 });

db.CommunityActions.createIndex({ "communityId": 1, "type": 1 });
db.CommunityActions.createIndex({ "targetId": 1 });
db.CommunityActions.createIndex({ "authorAddress": 1 });

db.CommunityModeration.createIndex({ "communityId": 1, "status": 1 });
db.CommunityModeration.createIndex({ "targetId": 1 });

db.CommunityInvites.createIndex({ "communityId": 1, "code": 1 });
db.CommunityInvites.createIndex({ "inviteeAddress": 1 });
```


## БД контента

```javascript
// 1. Transactions (Хранение всех транзакций из блокчейна)
{
  _id: ObjectId,
  txId: String,          // Hash транзакции
  type: Number,          // TxType из PocketTypes.h
  blockId: Number,
  blockNum: Number,
  height: Number,
  time: Date,
  authorAddress: String, // Адрес создателя транзакции
  // Специфичные поля из Registry и Payload
  data: {
    string1: String,     // lang, message и т.д.
    string2: String,     // name, caption и т.д.
    string3: String,     // avatar, message и т.д.
    string4: String,     // about, tags и т.д.
    string5: String,     // url, images и т.д.
    string6: String,     // pubkey, settings и т.д.
    string7: String,     // donations, url и т.д.
    int1: Number        // value, reason и т.д.
  },
  // Связи с другими транзакциями
  references: {
    regId1: String,     // ReferrerAddressId, RootTxId и т.д.
    regId2: String,     // ReferrerAddressId, RootTxId и т.д.
    regId3: String,     // RelayRootTxId и т.д.
    regId4: String,     // ParentRootTxId и т.д.
    regId5: String      // AnswerRootTxId и т.д.
  }
}

// 2. Users (Оптимизированная информация о пользователях)
{
  _id: ObjectId,
  address: String,       // Адрес пользователя
  txId: String,         // Транзакция создания аккаунта
  profile: {
    name: String,
    avatar: String,
    about: String,
    url: String,
    lang: String,
    pubkey: String,
    donations: Object,
    settings: Object
  },
  stats: {
    postsCount: Number,
    commentsCount: Number,
    followersCount: Number,
    followingCount: Number,
    rating: Number,
    karma: Number
  },
  badges: [{
    type: Number,       // Developer = 0, Shark = 1, Whale = 2, Moderator = 3
    receivedAt: Date,
    active: Boolean
  }],
  moderation: {
    isModerator: Boolean,
    banEndHeight: Number,
    restrictions: [String]
  },
  lastActivity: Date,
  createdAt: Date,
  updatedAt: Date
}

// 3. Posts (Оптимизированная структура для контента)
{
  _id: ObjectId,
  txId: String,
  type: Number,         // CONTENT_POST, CONTENT_VIDEO и т.д.
  authorId: ObjectId,   // Ссылка на Users
  content: {
    title: String,
    message: String,
    lang: String,
    tags: [String],
    media: [{
      type: String,     // image, video, audio
      url: String
    }],
    settings: Object
  },
  stats: {
    views: Number,
    likes: Number,
    dislikes: Number,
    commentsCount: Number,
    score: Number,
    boost: Number
  },
  moderation: {
    flags: [{
      txId: String,
      reason: Number,
      createdAt: Date
    }],
    verdict: {
      status: String,   // pending, approved, rejected
      votes: [{
        moderatorId: ObjectId,
        verdict: Boolean,
        txId: String
      }]
    }
  },
  blockchain: {
    height: Number,
    blockId: Number,
    blockNum: Number
  },
  createdAt: Date,
  updatedAt: Date
}

// 4. Comments (Оптимизированная структура комментариев)
{
  _id: ObjectId,
  txId: String,
  postId: ObjectId,     // Ссылка на Posts
  authorId: ObjectId,   // Ссылка на Users
  parentId: ObjectId,   // Для древовидных комментариев
  answerToId: ObjectId, // Для ответов на комментарии
  content: {
    message: String,
    edited: Boolean
  },
  stats: {
    likes: Number,
    dislikes: Number,
    replies: Number,
    score: Number
  },
  blockchain: {
    height: Number,
    blockId: Number,
    blockNum: Number
  },
  createdAt: Date,
  updatedAt: Date
}

// 5. Social (Социальные связи)
{
  _id: ObjectId,
  type: String,         // follow, block
  fromUserId: ObjectId,
  toUserId: ObjectId,
  txId: String,
  isPrivate: Boolean,   // Для приватных подписок
  createdAt: Date
}

// 6. Actions (Действия пользователей)
{
  _id: ObjectId,
  type: Number,         // Тип действия из TxType
  txId: String,
  userId: ObjectId,     // Кто совершил действие
  targetType: String,   // post, comment
  targetId: ObjectId,   // ID поста или комментария
  value: Number,        // Значение (для оценок)
  blockchain: {
    height: Number,
    blockId: Number,
    blockNum: Number
  },
  createdAt: Date
}

// 7. Feed (Оптимизированная коллекция для ленты)
{
  _id: ObjectId,
  userId: ObjectId,     // Для кого лента
  postId: ObjectId,     // Ссылка на пост
  score: Number,        // Рейтинг поста для этого пользователя
  type: String,         // global, subscribed, hot и т.д.
  createdAt: Date
}
```

```javascript
// Transactions
db.Transactions.createIndex({ "txId": 1 }, { unique: true });
db.Transactions.createIndex({ "type": 1 });
db.Transactions.createIndex({ "authorAddress": 1 });
db.Transactions.createIndex({ "height": -1 });
db.Transactions.createIndex({ "references.regId1": 1 });
db.Transactions.createIndex({ "references.regId2": 1 });
db.Transactions.createIndex({ "references.regId3": 1 });

// Users
db.Users.createIndex({ "address": 1 }, { unique: true });
db.Users.createIndex({ "profile.name": 1 });
db.Users.createIndex({ "stats.rating": -1 });
db.Users.createIndex({ "lastActivity": -1 });

// Posts
db.Posts.createIndex({ "txId": 1 }, { unique: true });
db.Posts.createIndex({ "authorId": 1 });
db.Posts.createIndex({ "content.tags": 1 });
db.Posts.createIndex({ "stats.score": -1 });
db.Posts.createIndex({ "createdAt": -1 });
db.Posts.createIndex({ "blockchain.height": -1 });

// Comments
db.Comments.createIndex({ "txId": 1 }, { unique: true });
db.Comments.createIndex({ "postId": 1 });
db.Comments.createIndex({ "authorId": 1 });
db.Comments.createIndex({ "parentId": 1 });
db.Comments.createIndex({ "answerToId": 1 });
db.Comments.createIndex({ "createdAt": -1 });

// Social
db.Social.createIndex({ "fromUserId": 1, "type": 1 });
db.Social.createIndex({ "toUserId": 1, "type": 1 });
db.Social.createIndex({ "txId": 1 }, { unique: true });

// Actions
db.Actions.createIndex({ "txId": 1 }, { unique: true });
db.Actions.createIndex({ "userId": 1 });
db.Actions.createIndex({ "targetId": 1 });
db.Actions.createIndex({ "type": 1 });

// Feed
db.Feed.createIndex({ "userId": 1, "type": 1, "score": -1 });
db.Feed.createIndex({ "postId": 1 });
```
