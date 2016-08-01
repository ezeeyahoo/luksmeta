[![License](http://img.shields.io/:license-lgpl2-blue.svg?style=flat-square)](http://www.gnu.org/licenses/lgpl-2.1.html)
[![Build Status](https://travis-ci.org/latchset/luksmeta.svg?branch=master)](https://travis-ci.org/latchset/luksmeta)
[![Code Coverage](http://codecov.io/github/latchset/luksmeta/coverage.svg?branch=master)](http://codecov.io/github/latchset/luksmeta?branch=master)
[![Coverity Scan Build Status](https://scan.coverity.com/projects/8706/badge.svg)](https://scan.coverity.com/projects/latchset-luksmeta)

# LUKSMeta

Welcome to LUKSMeta! LUKSMeta is a simple library for storing metadata in the LUKSv1 header. This library is licensed under the GNU LGPLv2+.

## Why LUKSMeta?

Some projects need to store additional metadata about a LUKS volume that is accessable before unlocking it. Two such examples are [USBGuard][usbguard] and [Tang][tang]. Fortunately, there is a gap in the LUKS header between the end of the slot area and the payload offset:

    +---------------+------------------+-----------------------------------------+----------------+
    | LUKSv1 header | LUKSv1 slots (8) |                                         | Encrypted Data |
    +---------------+------------------+-----------------------------------------+----------------+

LUKSMeta uses this hole to store additional metadata.

## How does LUKSMeta work?

LUKSMeta's on-disk format consists of a header block, followed by 0-8 data blocks. Each block is aligned to 4096 bytes. The LUKSMeta header contains a checksum (CRC32c) of itself and of each data block to detect data corruption. Each data block is also given a 16 byte UUID type to uniquely identify the contents of the block.

The end result looks like this on disk:

    +---------------+------------------+-----------------+-----------------------+----------------+
    | LUKSv1 header | LUKSv1 slots (8) | LUKSMeta header | LUKSMeta blocks (0-8) | Encrypted Data |
    +---------------+------------------+-----------------+-----------------------+----------------+

[usbguard]: https://github.com/dkopecek/usbguard
[tang]: https://github.com/latchset/tang