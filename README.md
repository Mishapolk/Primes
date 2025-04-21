# 100 Million Primes!

## [Full 100 Million Primes .txt File Download](https://www.mediafire.com/file/2vpk3ho0yeletsa/primes.txt/file)

### All Other Files (10, 100, 1k, 10k, 100k, 1m, 10m) Can Be Downloaded Here
(Github file size limit doesn't allow me to upload the 100 millon here)
<img width="901" alt="Screen Shot 2024-07-11 at 10 59 31 PM" src="https://github.com/user-attachments/assets/90269155-9fab-41dc-b0a1-b80de28c50b7">

Documentation on how it was done, the algorythm utilized, and a lot of other data will be posted below! (Most people are here just for the data, if any)

## Stats!

### Average Gaps Between Primes
Over the sample of 100 million, the global average is `20.380748`

### 5-Point Summary, Standard Deviation, LSRL 

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
## Thank you for reading!

Thanks to anyone who even got this far down on the page (probably no one), this was just something I was interested in, and thought I would publish my findings if anyone ever needs to use this data, so I figured I'd make it easily accessible. 

If you want more prime data, make sure to either check out [Walter-Fendt.com](https://www.walter-fendt.de/html5/men/primenumbers_en.htm) or [Compasso.free](http://compoasso.free.fr/primelistweb/page/prime/liste_online_en.php). These are both awesome websites for looking up primes and searching for patterns. Once again thank you for reading this till the end if you are here, and pls star :)

### Everything below is pointless wordspam for the SEO, nothing for you to find.

Prime, prime digits, prime integers, prime numbers, prime numerals, prime figures, list of prime numbers, catalog of prime numbers, directory of prime numbers, 100 million primes, 100 million prime numbers, 100 million prime digits, 10 million primes, 10 million prime numbers, 10 million prime digits, 1 million primes, 1 million prime numbers, 1 million prime digits, 100 thousand primes, 100 thousand prime numbers, 100 thousand prime digits, 10 thousand primes, 10 thousand prime numbers, 10 thousand prime digits, 1 thousand primes, 1 thousand prime numbers, 1 thousand prime digits, 100 primes, 100 prime numbers, 100 prime digits, 10 primes, 10 prime numbers, 10 prime digits, primes list, list of primes, primes catalog, ordered list of primes, sorted list of primes, arranged list of primes, randomly ordered primes, unordered primes, random prime numbers, list of primes, catalog of primes, directory of primes, prime number list, list of prime digits, list of prime numerals, list of prime numbers up to 100 million, 100 million prime numbers list, prime numbers list up to 100 million, top 10 million primes, 10 million top primes, leading 10 million primes, prime numbers up to 1 million, 1 million prime numbers, list of primes up to 1 million, find 100 thousand prime numbers, locate 100 thousand primes, discover 100 thousand prime numbers, 10 thousand primes list, list of 10 thousand primes, catalog of 10 thousand primes, list of 1 thousand prime numbers, 1 thousand primes catalog, directory of 1 thousand prime numbers, 100 prime numbers, list of 100 primes, 100 primes catalog, top 10 prime numbers, leading 10 primes, best 10 prime numbers, comprehensive prime number list, exhaustive list of primes, full prime numbers catalog, detailed list of primes, thorough prime numbers list, in-depth list of primes, prime numbers sequence, sequence of primes, primes progression, sequence of prime numbers, prime number sequence, prime progression, 100 million prime numbers list, list of 100 million primes, catalog of 100 million primes, list of top 10 million primes, 10 million top primes list, leading 10 million prime numbers, 1 million prime numbers list, catalog of 1 million primes, list of 1 million prime digits, 100 thousand primes sequence, sequence of 100 thousand primes, progression of 100 thousand prime numbers, list of top 10 thousand primes, 10 thousand top primes list, leading 10 thousand prime numbers, 1 thousand prime numbers sequence, sequence of 1 thousand primes, progression of 1 thousand prime numbers, list of 100 prime numbers, catalog of 100 primes, directory of 100 prime numbers, list of top 10 primes, top 10 primes catalog, leading 10 prime numbers, prime numbers directory, directory of prime numbers, prime numbers register, prime numbers catalog, catalog of prime numbers, prime numbers archive, prime numbers index, index of primes, prime index, extensive list of primes, broad list of primes, comprehensive primes catalog, list of large primes, catalog of large primes, directory of large prime numbers, small prime numbers list, list of small primes, catalog of small prime numbers, huge prime numbers list, list of huge primes, catalog of huge prime numbers, ordered prime numbers list, sorted prime numbers catalog, arranged list of prime numbers, randomly ordered list of primes, unordered primes catalog, random list of primes, primes arranged list, arranged list of primes, primes ordered list, unordered list of prime numbers, unordered primes catalog, random prime numbers list, prime number catalog, catalog of prime numbers, prime numbers directory, prime number database, database of prime numbers, prime numbers repository, prime number register, register of prime numbers, prime numbers index, prime number archive, archive of prime numbers, prime numbers catalog, collection of prime numbers, prime numbers collection, prime numbers anthology, prime number chart, chart of prime numbers, prime numbers diagram, prime number graph, graph of prime numbers, prime numbers plot, prime number table, table of prime numbers, prime numbers matrix, prime number data, data of prime numbers, prime numbers statistics, prime number statistics, statistics of prime numbers, prime numbers data, prime number analysis, analysis of prime numbers, prime numbers evaluation, prime number details, details of prime numbers, prime numbers information

