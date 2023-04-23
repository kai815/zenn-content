---
title: "「初めてのGraphQL」の内容の実装をNestjsでやってみた"
emoji: "♠️"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["GraphQL", "Nestjs", "TypeScript"]
published: true
---

[「初めてのGraphQL」](https://www.oreilly.co.jp/books/9784873118932/)という書籍を読んだのですが、その中で出てくる、写真投稿アプリ?の実装を Nestjs,TypeScript で実装してみました。
実装をする際のポイントとなりそうなところをメモ的に書いていきたいと思います。自分と同じように、「初めてのGraphQL」の内容をNestjsでやってみようかなぁと思っている方の参考になれば嬉しいです。

ソースコードはGitHubにあげており、以下のやつのbackend-nestjsのディレクトリです。そちらも参考にしていただけますと、幸いです。

https://github.com/kai815/learn-graphql-app

※実装していた時、node.jsのバージョンはv16.13.1でした。

## コードファーストかスキーマファーストか

コードファーストはコードの内容を元にスキーマファイルを作成してくれるアプローチで、スキーマファーストはスキーマの内容を正として、中のコードを開発者が実装していきます。

コードファーストのメリットはこちらの記事が大変参考になります。
https://logmi.jp/tech/articles/326592

この記事を読んだ影響もあり、コードファーストで実装してみました。

### コードファーストな実装の仕方

どうやってコードファーストで実装するかを書きます。
以下の`app.module.ts`の`autoSchemaFile`の設定をします。
`autoSchemaFile`に設定されたpathに、自動的にスキーマファイルが生成されるようになります。

```ts:backend-nestjs/src/app.module.ts
// ※関係なさそうなところは割愛してます
import { Module } from '@nestjs/common';
import { GraphQLModule } from '@nestjs/graphql';
import { ApolloDriver, ApolloDriverConfig } from '@nestjs/apollo';


@Module({
  imports: [
    GraphQLModule.forRoot<ApolloDriverConfig>({
      driver: ApolloDriver,
      autoSchemaFile: path.join(process.cwd(), "src/schema.gql"),
      // sortSchema: true, これするとabcd順というか辞書順になるようです
    }),
  ],
})
export class AppModule {}

```

そうすることで、デコレーターをもとに`schema.gql`を自動で更新してくれるようになります。
以下を見ると、`user.resolver.ts`の`allUsers`のQueryをコメントアウトすると、`schema.gql`の`allUsers`というQueryが消えてます。

![scheama-first](https://storage.googleapis.com/zenn-user-upload/883cac26a4b3-20230423.gif)

## photoからuserを参照するなど別のモデルを参照したい時
例えば以下のようなqueryで、`postedBy`には、`User`が入る時のイメージです。
```graphql
query allPhotosQuery {
  allPhotos {
    id
    name
    description
    postedBy{
      githubLogin
    }
  }
}

```

このように別のモデルを参照したい時には、`ResolveField`のデコレーターを使うことで、一応は取得することができます。

```ts: backend-nestjs/src/components/photos/photo.resolver.ts
//* 関係なさそうなところは省略しております。
@Resolver((of) => PhotoModel)
export class PhotosResolver {
  constructor(private photoService: PhotoService,private userService: UserService) {}
  @Query(() => [PhotoModel], { name: 'allPhotos', nullable: true })
  async allPhotos() {
    const result = await this.photoService.allPhoto()
    return result
  }
  // ここでuserを取得しにいっている
  @ResolveField('postedBy', returns =>UserModel)
  async getPostedBy(@Parent() photo: Photo) {
    const { userId } = photo;
    return this.userService.findOne({githubLogin: userId});
  }
}

```

ただ、これだとphotoの数だけ、userを取得しにいくことになり、N+1問題になります。
N+1問題に対処するにはDataloaderを用いるなどの方法がある様です。
今回の「初めてのGraphQL」の中の実装では触れられていなかったので、実装については割愛させていただきます。

以下の記事とかが参考になりそうです。

https://engineering.mercari.com/blog/entry/20210818-mercari-shops-nestjs-graphql-server/

## MongoDBとの繋ぎこみ

`@nestjs/mongoose` `mongoose`をインストールします。
```
yarn add @nestjs/mongoose mongoose
```
そして、`app.module.ts`に設定を記載します。
```ts:backend-nestjs/src/app.module.ts
// ※関係なさそうなところは割愛してます。
import { Module } from '@nestjs/common';
import { MongooseModule } from '@nestjs/mongoose';

@Module({
  imports: [MongooseModule.forRoot("ここにdbのホスト")],
})
export class AppModule {}
```

photoのMongoDB用のスキーマを定義します。
```ts:backend-nestjs/src/components/photos/schemas/photo.schema.ts
import { Prop, Schema, SchemaFactory } from '@nestjs/mongoose';
import { HydratedDocument } from 'mongoose';

export type PhotoDocument = HydratedDocument<Photo>;

@Schema()
export class Photo {
  @Prop()
  name: string;

  @Prop()
  category: number;

  @Prop()
  url:string;

  @Prop()
  description: string;

  @Prop()
  userId:string;

  @Prop()
  created:string
}

export const PhotoSchema = SchemaFactory.createForClass(Photo);
```

`PhotoService`の中で`Photo`の取得などを行っており、以下の様に書きます。
```ts:backend-nestjs/src/components/photos/photo.service.ts
@Injectable()
export class PhotoService {
  constructor(@InjectModel(Photo.name) private photoMongoModel: Model<PhotoDocument>) {}

  // 全件取得のメソッド
  async allPhoto(): Promise<Photo[]> {
    const result = this.photoMongoModel.find().exec()
    return result
  }
}
```
`PhotoService`で`PhotoModel`関連を使うために以下の様に、MongooseModule.forFeatureを用いて、modelの定義をする必要があります。

```ts:backend-nestjs/src/components/photos/photos.module.ts
import { Module } from '@nestjs/common';
import {PhotosResolver} from "./photo.resolver";
import {PhotoService} from "./photo.service";
import {UserService} from "@/components/users/user.service";
import {Photo,PhotoSchema} from "@/components/photos/schemas/photo.schema";
import {MongooseModule} from "@nestjs/mongoose";
import {User, UserSchema} from "@/components/users/schemas/user.schema";
import {HttpModule} from "@nestjs/axios";

@Module({
  imports: [
    HttpModule,
    //ここ
    MongooseModule.forFeature(
    [
      { name: Photo.name, schema: PhotoSchema },
      { name:User.name, schema: UserSchema }
    ]
  )],
  providers:[PhotosResolver,PhotoService,UserService]
})
export class PhotosModule {}

```
これがないと、`Error: Nest can't resolve dependencies of the PhotoService (?). Please make sure that the argument PhotoModel at index [0] is available in the PhotosModule context.`
というエラーがでます。

### MongoDBのクラウドサービス
または私は今回の実装をするに当たり、以下のサービスを使いました。面倒な環境構築も必要ないので、手軽に試せておすすめです。
https://www.mongodb.com/ja-jp

## Subscriptionの実装
SubscriptionとはGraphQLサーバーにデータ変化などの特定のイベントが生じるたびにクライアント側に通知（データ）を送る機能のことです。
チャットなどのリアルタイム性が求められるものに使われるかもしれません。

以下の実際に動かした動画です。
![subscription](https://storage.googleapis.com/zenn-user-upload/310cc82a4512-20230423.gif)
左側は`newUser`を`Subscription`しているのですが、右側で`User`を追加する`Mutation`を行いました。
その際に、左側で追加されたことがリアルタイムで通知されていることがわかるかと思います。

それでは実装に入っていきます。
`graphql-subscriptions`をインストールします。
```
yarn add graphql-subscriptions
```

`app.module.ts`に設定を追加します。
```diff ts:backend-nestjs/src/app.module.ts
@Module({
  imports: [
    GraphQLModule.forRoot<ApolloDriverConfig>({
      driver: ApolloDriver,
+      installSubscriptionHandlers: true,
})
export class AppModule {}
```

`user.resolver.ts`を修正していきます。
まずは、Subscriptionする側の実装です。
```diff ts:backend-nestjs/src/components/users/user.resolver.ts
+const pubSub = new PubSub();

@Resolver((of) => UserModel)
export class UsersResolver {
  constructor(private userService: UserService,private photoService: PhotoService) {}
+ @Subscription((returns) => UserModel,{name:'newUser'})
+ newUser() {
+   return pubSub.asyncIterator('newUser');
+ }
  @ResolveField('postedPhotos', returns => [PhotoModel])
  async getPosts(@Parent() user: UserModel) {
    const { githubLogin } = user;
    return this.photoService.findAll({ userId: githubLogin });
  }
}
```

そして通知する側の実装です。
```diff ts:backend-nestjs/src/components/users/user.resolver.ts
@Resolver((of) => UserModel)
export class UsersResolver {
  constructor(private userService: UserService,private photoService: PhotoService) {}
  @Mutation(()=>[UserModel],{name:'addFakeUsers',nullable:true})
  async addFakeUsers(@Args('count') count:number){
    const result =  await this.userService.addFakeUsers(count)
    result.forEach((createdUser)=>{
+     pubSub.publish('newUser',{newUser:createdUser})
    })
    return result
  }
  @ResolveField('postedPhotos', returns => [PhotoModel])
  async getPosts(@Parent() user: UserModel) {
    const { githubLogin } = user;
    return this.photoService.findAll({ userId: githubLogin });
  }
}
```

注意点としては、
`pubSub.asyncIterator`の引数にトリガー名を渡すのですが、そこが`pubSub.publish`の第一引数のトリガー名と一緒になっていないといけないことです。
また、publishの第二引数が`asyncIterator`の戻り値になります。


## 最後に

「初めてのGraphQL」の写真投稿アプリの内容をNestjsで実装する際のポイントを書いてみました。
また本の内容は、GraphQLの概要をつかむには良かったと思います。
私自身、GraphQLもNestjsもまだ勉強中の身ですが、この記事が誰かの役に立ったら嬉しいです。