# 100 Million Primes!

## [Full 100 Million Primes .txt File Download](https://www.mediafire.com/file/2vpk3ho0yeletsa/primes.txt/file)

### All Other Files (10, 100, 1k, 10k, 100k, 1m, 10m) Can Be Downloaded Here
(Github file size limit doesn't allow me to upload the 100 millon here)
<img width="901" alt="Screen Shot 2024-07-11 at 10 59 31 PM" src="https://github.com/user-attachments/assets/90269155-9fab-41dc-b0a1-b80de28c50b7">

Documentation on how it was done, the algorythm utilized, and a lot of other data will be posted below! (Most people are here just for the data, if any)

## Stats!

To start off, here are some statistics around the first 10^[1...7], including the 5 point summary, standart deviation, and Least Squares Regression Line.

### 10

- Min - 2.0
- Q1 - 5.5
- Q2 - 12.0
- Q3 - 18.5
- Max - 29.0
- Mean - 12.9
- SD - 9.024041962077378
- LSRL - y = 2.94x + -3.27


### 100

- Min - 2.0
- Q1 - 100.0
- Q2 - 231.0
- Q3 - 380.0
- Max - 541.0
- Mean - 241.33
- SD - 160.82835162641157
- LSRL - y = 5.53x + -37.97


### 1,000

- Min - 2.0
- Q1 - 1593.5
- Q2 - 3576.0
- Q3 - 5695.0
- Max - 7919.0
- Mean - 3682.913
- SD - 2344.091671243724
- LSRL - y = 8.11x + -374.88


### 10,000

- Min - 2.0
- Q1 - 22334.0
- Q2 - 48615.0
- Q3 - 76208.5
- Max - 104729.0
- Mean - 49616.5411
- SD - 30793.72378462149
- LSRL - y = 10.66x + -3690.89


### 100,000

- Min - 2.0
- Q1 - 287132.0
- Q2 - 611955.0
- Q3 - 951169.0
- Max - 1299709.0
- Mean - 622606.98721
- SD - 380627.4882967374
- LSRL - y = 13.18x + -36413.41


### 1,000,000

- Min - 2.0
- Q1 - 3497865.5
- Q2 - 7368789.0
- Q3 - 11381624.0
- Max - 15485863.0
- Mean - 7472966.967499
- SD - 4523196.24975472
- LSRL - y = 15.66x + -359395.59

  
### 10,000,000

- Min - 2.0
- Q1 - 41161748.0
- Q2 - 86028139.0
- Q3 - 132276696.5
- Max - 179424673.0
- Mean - 87053041.4842019
- SD - 52322599.112646334
- LSRL - y = 18.12x + -3555031.37
  

### 100,000,000

- Min - 2.0
- Q1 - 472882043.5
- Q2 - 982451680.0
- Q3 - 1505776945.0
- Max - 2038074743.0
- Mean - 992628510.561837
- SD - 593533642.8238806
- LSRL - y = 20.56x + -35251479.41

## How was all this data found? 

All of the 100 million primes were found within aproximately 1.2 hours, of a running a rust script on a rather old and unoptimized system. This is the script I used to find the primes:

```
use std::collections::HashSet;
use std::fs::File;
use std::io::{self, Write};
use std::sync::{Arc, Mutex};
use std::thread;
use std::time::{Duration, Instant};

fn precompute_primes(limit: usize) -> Vec<usize> {
    let mut is_prime = vec![true; limit + 1];
    is_prime[0] = false;
    is_prime[1] = false;
    for p in 2..=((limit as f64).sqrt() as usize) {
        if is_prime[p] {
            for i in (p * p..=limit).step_by(p) {
                is_prime[i] = false;
            }
        }
    }
    (2..=limit).filter(|&p| is_prime[p]).collect()
}

fn is_prime(n: usize, primes: &[usize], memo: &mut HashSet<usize>) -> bool {
    if memo.contains(&n) {
        return true;
    }
    if n <= 1 {
        return false;
    }
    for &prime in primes {
        if prime * prime > n {
            break;
        }
        if n % prime == 0 {
            return false;
        }
    }
    memo.insert(n);
    true
}

fn generate_primes(primes: Arc<Mutex<Vec<usize>>>, memo: Arc<Mutex<HashSet<usize>>>, stop_flag: Arc<Mutex<bool>>, start_time: Instant) {
    let mut candidate = {
        let primes = primes.lock().unwrap();
        *primes.last().unwrap() + 1
    };

    loop {
        {
            let mut primes = primes.lock().unwrap();
            let mut memo = memo.lock().unwrap();
            if is_prime(candidate, &primes, &mut memo) {
                primes.push(candidate);
            }
        }
        candidate += 1;

        if *stop_flag.lock().unwrap() {
            break;
        }

        let elapsed = start_time.elapsed();
        let primes_count = primes.lock().unwrap().len();
        print!("\rPrimes found: {} | Time running: {:.2?}", primes_count, elapsed);
        io::stdout().flush().unwrap();
    }
}

fn main() {
    let limit = 10000;
    let primes = Arc::new(Mutex::new(precompute_primes(limit)));
    let memo = Arc::new(Mutex::new(HashSet::new()));
    let stop_flag = Arc::new(Mutex::new(false));
    let start_time = Instant::now();

    let primes_clone = Arc::clone(&primes);
    let memo_clone = Arc::clone(&memo);
    let stop_flag_clone = Arc::clone(&stop_flag);

    let handle = thread::spawn(move || {
        generate_primes(primes_clone, memo_clone, stop_flag_clone, start_time);
    });

    println!("Press Enter to stop...");
    let _ = io::stdin().read_line(&mut String::new()).unwrap();

    {
        let mut stop = stop_flag.lock().unwrap();
        *stop = true;
    }

    handle.join().unwrap();

    let primes = primes.lock().unwrap();
    let mut file = File::create("primes.txt").expect("Unable to create file");
    for &prime in primes.iter() {
        writeln!(file, "{}", prime).expect("Unable to write data");
    }
    let elapsed = start_time.elapsed();
    writeln!(file, "Primes found: {}", primes.len()).expect("Unable to write data");
    writeln!(file, "Time running: {:.2?}", elapsed).expect("Unable to write data");

    println!("\nPrimes exported to primes.txt");
}
```

Here's a quick breakdown of what each function does:
