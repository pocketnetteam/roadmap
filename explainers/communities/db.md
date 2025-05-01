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
