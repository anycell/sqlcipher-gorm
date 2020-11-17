# sqlcipher-gorm
#### Sqlcipher driver for go-gorm

#### Sample Code:

```
import (
	"fmt"
	"github.com/anycell/sqlcipher-gorm"
	"gorm.io/gorm"
	"net/url"
)

type Product struct {
	gorm.Model
	Code  string
	Price uint
}

func main() {
	key := url.QueryEscape("my password")
	dbname := fmt.Sprintf("db?_pragma_key=x'%s'&_pragma_cipher_page_size=4096", key)


	db, err := gorm.Open(sqlcipher.Open(dbname), &gorm.Config{})
	if err != nil {
		panic("failed to connect database")
	}

	// Migrate the schema
	db.AutoMigrate(&Product{})

	// Create
	db.Create(&Product{Code: "D42", Price: 100})

	// Read
	var product Product
	//db.First(&product, 1) // find product with integer primary key
	db.First(&product, "code = ?", "D42") // find product with code D42

	// Update - update product's price to 200
	db.Model(&product).Update("Price", 200)
	// Update - update multiple fields
	db.Model(&product).Updates(Product{Price: 200, Code: "F42"}) // non-zero fields
	db.Model(&product).Updates(map[string]interface{}{"Price": 200, "Code": "F42"})

	// Delete - delete product
	db.Delete(&product, 1)
}
```

### Reference

- [go-gorm sqlite](https://github.com/go-gorm/sqlite)
- [go-sqlcipher](https://github.com/mutecomm/go-sqlcipher)