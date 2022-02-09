---
title: "Prismaでハマったところメモ"
emoji: "😎"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["Prisma", "TypeScript"]
published: false
---

## 関連するテーブルがある際の削除

Foreign key constraint failed on the field: `UserTask_taskId_fkey (index)

https://www.prisma.io/docs/concepts/components/prisma-schema/relations/referential-actions#referential-action-defaults

onDelete: SetNull

Null constraint violation on the fields: (`taskId`)
