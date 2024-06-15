#!/usr/bin/env nu

if not (is-admin) {
  error make -u {
    msg: 'you have to be root',
  }
}

use std

def 'archorg join' [] {
  prepend 'iso' | path join | {
    scheme: 'https',
    host: 'archive.archlinux.org',
    path: $in,
  } | url join
}

let temp = mktemp -d
std log debug $'work in ($temp)'
let version = archorg join | http get $in | parse -r '<a href="(?<v>[^/]+)/">' | $in.v | last
std log debug $'the latest version is ($version)'
let architecture = 'x86_64'
std log debug $'the target architecture is ($architecture)'
mut ok = false
mut extension = ''
mut file = ''
mut option = ''
mut tarball = ''

for mode in [
  [extension, option];
  ['zst', '--zstd'],
  ['gz', '-z']
] {
  $extension = $mode.extension
  $file = $'archlinux-bootstrap-($version)-($architecture).tar.($extension)'
  let url = [$version, $file] | archorg join
  std log debug $'try to get ($url)'
  let response = http get -f -e $url

  match $response.status {
    200 => {
      std log debug $'use ($url)'
      $option = $mode.option
      $tarball = $response.body
      $ok = true
      break
    },
    404 => {
      std log debug $'($url) is nou found'
      continue
    },
    $status => {
      error make -u {
        msg: $'($url) responds with ($status)',
      }
    },
  }
}

if not $ok {
  error make -u {
    msg: 'cannot get a bootstrap tarball',
  }
}

let extension = $extension
let bootstrap_file = $file
let option = $option
let tarball = $tarball
let calculated_hash = $tarball | hash sha256
let hash_file = 'sha256sums.txt'
let hash_url = [$version, $hash_file] | archorg join
std log debug $'query a hash from ($hash_url)'
let expected_hash = $hash_url | http get $in | parse -r $"\(?<c>.{64})  ($bootstrap_file)" | $in.c.0

if $calculated_hash != $expected_hash {
  error make -u {
    msg: $'the hash of ($bootstrap_file) is ($calculated_hash), but ($hash_file) says it is ($expected_hash)',
  }
}

std log debug $'the sha256 of ($bootstrap_file) is ($calculated_hash)'
std log info $'extract ($bootstrap_file)'
$tarball | tar -x $option -f - -C $temp
let rootfs_file = $'archlinux-rootfs-($version)-($architecture).tar.($extension)'
std log info $'create ($rootfs_file)'
[$temp, 'root.x86_64'] | path join | tar -c -a -f $rootfs_file -C $in --transform 's\^./\\' .
std log debug $'remove ($temp)'
rm -r $temp
