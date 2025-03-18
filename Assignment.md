1. Definisi Interface Task

package main

import (
"fmt"
"math/rand"
"reflect"
"time"
)

type Task interface {
Execute() error
Info() string
}

2. Struct Dasar untuk Tugas (BaseTask)

type BaseTask struct {
ID string
CreatedAt time.Time
Description string
}

3. Task untuk mengirim Email

type EmailTask struct {
BaseTask
}

func (e _EmailTask) Execute() error {
fmt.Printf("[EmailTask] Mengirim email... (ID: %s)\n", e.ID)
time.Sleep(time.Duration(rand.Intn(3)) _ time.Second)
if rand.Float32() < 0.2
{
return fmt.Errorf("gagal mengirim email")
}
fmt.Println("[EmailTask] Email berhasil dikirim!")
return nil
}

func (e \*EmailTask) Info() string {
return fmt.Sprintf("EmailTask - %s", e.Description)
}

4. Task untuk mengirim SMS
   type SMSTask struct {
   BaseTask
   }

func (s _SMSTask) Execute() error {
fmt.Printf("[SMSTask] Mengirim SMS... (ID: %s)\n", s.ID)
time.Sleep(time.Duration(rand.Intn(3)) _ time.Second)
if rand.Float32() < 0.2 {
return fmt.Errorf("gagal mengirim SMS")
}
fmt.Println("[SMSTask] SMS berhasil dikirim!")
return nil
}

func (s \*SMSTask) Info() string {
return fmt.Sprintf("SMSTask - %s", s.Description)
}

5. Task untuk mengirim Report
   type ReportTask struct {
   BaseTask
   }

func (r _ReportTask) Execute() error {
fmt.Printf("[ReportTask] Membuat laporan... (ID: %s)\n", r.ID)
time.Sleep(time.Duration(rand.Intn(3)) _ time.Second)
if rand.Float32() < 0.2 {
return fmt.Errorf("gagal membuat laporan")
}
fmt.Println("[ReportTask] Laporan berhasil dibuat!")
return nil
}

func (r \*ReportTask) Info() string {
return fmt.Sprintf("ReportTask - %s", r.Description)
}

4. Manager untuk Menangani Tugas (TaskManager)

type TaskManager struct {
Tasks []Task
FailedTasks []Task // Buat nyimpen task yang gagal
}

// Method buat nambahim tugas ke daftar
func (tm \*TaskManager) AddTask(task Task) {
tm.Tasks = append(tm.Tasks, task)
}

// Method buat ngejalanin semua tugas
func (tm \*TaskManager) ExecuteAll() {
fmt.Println("\n=== Menjalankan Semua Tugas ===")
for \_, task := range tm.Tasks {
start := time.Now()
taskType := reflect.TypeOf(task).Elem().Name() // Dapetin nama struct pake reflection
fmt.Printf("Menjalankan %s (ID: %s)\n", taskType, task.Info())

    	err := task.Execute()
    	duration := time.Since(start)

    	if err != nil {
    		fmt.Printf("❌ Gagal: %s (Durasi: %s)\n", err.Error(), duration)
    		tm.FailedTasks = append(tm.FailedTasks, task) // Simpan yang gagal
    	} else {
    		fmt.Printf("✅ Berhasil! (Durasi: %s)\n", duration)
    	}
    }

}

// Method buat ngejalanin ulang task yang gagal
func (tm \*TaskManager) RetryFailedTasks() {
if len(tm.FailedTasks) == 0 {
fmt.Println("\nTidak ada tugas yang gagal!")
return
}

    fmt.Println("\n=== Menjalankan Ulang Tugas yang Gagal ===")
    retryTasks := tm.FailedTasks
    tm.FailedTasks = nil // Reset daftar task gagal

    for _, task := range retryTasks {
    	start := time.Now()
    	taskType := reflect.TypeOf(task).Elem().Name()
    	fmt.Printf("🔄 Mengulang %s (ID: %s)\n", taskType, task.Info())

    	err := task.Execute()
    	duration := time.Since(start)

    	if err != nil {
    		fmt.Printf("❌ Masih gagal: %s (Durasi: %s)\n", err.Error(), duration)
    		tm.FailedTasks = append(tm.FailedTasks, task) // Simpan lagi kalau masih gagal
    	} else {
    		fmt.Printf("✅ Berhasil setelah diulang! (Durasi: %s)\n", duration)
    	}
    }

}

5. Program Utama (main)

func main() {
rand.Seed(time.Now().UnixNano()) // Seed random biar hasil acak tiap jalanin

    manager := TaskManager{}

    // Tambahin beberapa tugas ke manager
    manager.AddTask(&EmailTask{BaseTask{"1", time.Now(), "Kirim email ke customer"}})
    manager.AddTask(&SMSTask{BaseTask{"2", time.Now(), "Kirim OTP ke user"}})
    manager.AddTask(&ReportTask{BaseTask{"3", time.Now(), "Buat laporan bulanan"}})

    // Jalankan semua tugas
    manager.ExecuteAll()

    // Coba ulangi tugas yang gagal
    manager.RetryFailedTasks()

}
