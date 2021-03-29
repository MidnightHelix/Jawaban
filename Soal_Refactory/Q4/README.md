
## Soal 4

What will be printed when the code below is executed?
1
And fix the issue to assure that `len(m)` is printed as 10.

```
package main

import (
	"sync"
	"time"
)

const N = 10

func main() {
	m := make(map[int]int)

	wg := &sync.WaitGroup{}
	mu := &sync.Mutex{}
        wg.Add(N)
	for i := 0; i < N; i++ {
		go func() {
			defer wg.Done()
			mu.Lock()
			m[i] = i
			mu.Unlock()
		}()
	}
	wg.Wait()
	println(len(m))
	time.Sleep(20 * time.Second)
}
```

Pertama kali dijalankan, program akan menampilkan angka 1 atau 2 tergantung dari keadaan mesin, hal itu disebabkan karena keadaan race condition, goroutines berjalan secara independen, sehingga saat iterasi for selesai, masih ada goroutine yang belum selesai dijalankan. Ketika iterasi for tersebut lebih dahulu selesai, GO runtime akan mentransfer memory ke heap sehingga goroutine yang belum selesai masih bisa merujuk pada variabel i pada iterasi for, namun biasanya memory yang di transfer ke heap merujuk pada nilai i pada iterasi terakhir yaitu 10, sehingga goroutine yang belum selesai dijalankan akan menggunakan nilai i yang salah, sehingga hasil output program juga tidak sesuai dengan harapan

```
	go func(i int) {
			defer wg.Done()
			mu.Lock()
			m[i] = i
			mu.Unlock()
		}(i)
```
Dengan menambahkan paramater i kepada anonymous function dan variabel iterasi i pada closure, sehingga goroutine akan merujuk pada nilai closure tersebut sehingga goroutine akan menggunakan nilai i yang seharusnya.