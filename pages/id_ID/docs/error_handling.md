---
title: Penanganan Kesalahan
layout: page
---
Sebagai Gantinya, penanganan kesalahan itu penting.

Anda disarankan untuk melakukan pengecekan kesalahan setelah setiap [Metode Langsung](/docs/method_chaining.html#Immediate-Methods)

## Penanganan Kesalahan

Kesalahan penanganan di GORM berbeda dengan kode Go idiomat karena API berantai, namun masih cukup mudah untuk melakukannya.

If there are any error happened, GORM will set it to `*gorm.DB`'s `Error` field, you could check it like this:

```go
if err := db.Where("name = ?", "jinzhu").First(&user).Error; err != nil {
    // error handling...
}
```

Atau

```go
if result := db.Where("name = ?", "jinzhu").First(&user); result.Error != nil {
    // error handling...
}
```

## Kesalahan

It is common several errors happened during processing data, GORM provides an API to return all happened errors as a slice

```go
// If there are more than one error happened, `GetErrors` returns them as `[]error`
db.First(&user).Limit(10).Find(&users).GetErrors()

fmt.Println(len(errors))

for _, err := range errors {
  fmt.Println(err)
}
```

## RecordNotFound Error

GORM provides a shortcut to handle `RecordNotFound` error, if there are several errors happened, it will check each error if any of them is `RecordNotFound` error.

```go
// Check if returns RecordNotFound error
db.Where("name = ?", "hello world").First(&user).RecordNotFound()

if db.Model(&user).Related(&credit_card).RecordNotFound() {
  // record not found
}

if err := db.Where("name = ?", "jinzhu").First(&user).Error; gorm.IsRecordNotFoundError(err) {
  // record not found
}
```