# Purpose
For a given comapny name, try a large variety of permutations in order to guess AWS S3 bucket names, identifying those that are public, printing them to the screen and open_buckets.txt.

For a given set of chars (e.g. -r abc123) and a number range (e.g. 3-5), generate random strings within the length range, using the given set of chars, to find public buckets, printing them to the screen and open_buckets.txt.

For a given ser of chars (e.g. -cp abc123) and a number of characters (e.g. -c 4), search all possible permutations of all combinations of length 4.

# Reason
Companies and individuals, far too often, have public S3 buckets with client data or PII in them.  Details of my adventures are here: https://www.mattsvensson.com/nerdings/2017/7/27/amazon-s3-bucket-searching-and-scraping

# Use - List
#Single threaded scan of a given company name<br>
./find_public_buckets.py -t 1 -s "This Company Name"

#Single threaded scan of a given company name, changing the output file<br>
./find_public_buckets.py -t 1 -s "This Company Name" -o thiscompanyname.log

#Dual threaded scan of the example file (company_names.txt)<br>
./find_public_buckets.py -t 2 -l comapny_names.txt

#Dual threaded scan of the example file (company_names.txt), starting after "Harris Corporation"<br>
./find_public_buckets.py -t 2 -l comapny_names.txt -a "Harris Corporation"

#Dual threaded scan of the example file (company_names.txt), starting after the 3rd line (using line 1 as the first)<br>
./find_public_buckets.py -t 2 -l comapny_names.txt -n 3

#Dual threaded scan of the example file (company_names.txt), printing every guessed bucketname <br>
./find_public_buckets.py -t 2 -l comapny_names.txt -p

# Use - Random Strings
#Random strings with lowercase and numbers, 4 char long <br>
./find_public_buckets.py -r abcdefghijklmnopqrstuvwxyz0123456789 -c 4

#Multi-threaded random strings with lowercase and numbers, 4 char long <br>
./find_public_buckets.py -r abcdefghijklmnopqrstuvwxyz0123456789 -c 4 -t 2

#Random strings with lowercase letters, 3-5 chars long. <br>
./find_public_buckets.py -r abcdefghijklmnopqrstuvwxyz -cr 3-5

# Use - All Permutations of a set of chars, for a given length
#Random strings with lowercase and numbers, 4 char long <br>
./find_public_buckets.py -cp abcdefghijklmnopqrstuvwxyz0123456789 -c 4

#Multithreaded random strings with lowercase and numbers, 4 char long <br>
./find_public_buckets.py -cp abcdefghijklmnopqrstuvwxyz0123456789 -c 4 -t 2

#Multithreaded random strings with lowercase and numbers, 4 char long, starting after "bcd", i.e. starting at "bde" <br>
./find_public_buckets.py -cp abcdefghijklmnopqrstuvwxyz0123456789 -c 4 -t 2 -a bcd

#Random strings with lowercase and numbers, 4 char long, starting after "a999" and stoping at "caaa" non-inclusive<br>
./find_public_buckets.py -cp abcdefghijklmnopqrstuvwxyz0123456789 -c 4 -a a999 -f caaa

# Output -p modifier to pring New Guesses
Without the -p modifier, you will see a progressbar, like below<br>
  0% [&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;] 28/21,147   3/sec   eta 1h 40m 22s

With the -p modifier, you will see the same progress bar printed (with updated) and every new guessed name you try (not found in buckets_found.txt.<br>
  0% [&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;] 1/1,064   1/sec   eta 10m 49s     https://s3.amazonaws.com/Test<br>
  0% [&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;] 2/1,064   1/sec   eta 16m 16s     https://s3.amazonaws.com/Test-Company<br>
  0% [&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;] 3/1,064   1/sec   eta 12m 15s     https://s3.amazonaws.com/Test-Company-archive<br>
  0% [&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;] 4/1,064   1/sec   eta 10m 13s     https://s3.amazonaws.com/Test-Company-backup<br>
  0% [&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;] 5/1,064   1/sec   eta 9m 4s     https://s3.amazonaws.com/Test-Company-bak<br>
  0% [&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;] 6/1,064   2/sec   eta 8m 14s     https://s3.amazonaws.com/Test-Company-beta<br>

# Notes
- On lists, I would recommend running this inside of tmux or screen with the -p option so that you can see the current comapny being scanned in case an error is thrown and it stops or you want to manually stop it.
- Buckets that exist will be written to found/buckets_found.txt in the root folder
- The "scanned" folder contains prior lists that you have scanned.  All names (by line) in these files will be skipped during the scan, to prevent re-running names on random lists you try.
- On an AWS EC2 t2.micro instance, I was able to search 30 names/sec per thread on a list of names.
