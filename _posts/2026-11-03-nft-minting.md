---
title: "NFT Minting on Sepolia"
date: 2026-11-03 12:00:00 +0900
categories: ["블로그/기술문서"]
tags: [Blockchain, Ethereum, NFT, ERC721, IPFS, Sepolia]
---

## 1. 실습 개요

블록체인 응용 과목에서 NFT의 구조와 동작 원리를 학습하고 Ethereum의 Sepolia 테스트넷에서 직접 NFT를 발행하는 실습을 진행하였다.

스마트 컨트랙트를 배포하고 NFT를 민팅한 뒤 Etherscan과 MetaMask를 통해 발행 결과를 확인하였다.

## 2. 실습 도구

- [Remix IDE](https://remix.ethereum.org/)
- [MetaMask](https://metamask.io/)
- [Pinata](https://pinata.cloud/)
- [IPFS](https://ipfs.tech/)
- [Sepolia Etherscan](https://sepolia.etherscan.io/)

## 3. 실습 과정

1. NFT에 사용할 이미지를 IPFS에 업로드하였다.
2. NFT의 정보를 담은 JSON 형식의 메타데이터를 생성하고 업로드하였다.
3. Remix IDE를 활용하여 NFT 스마트 컨트랙트를 Sepolia 테스트넷에 배포하였다.
4. 스마트 컨트랙트의 민팅 기능을 사용하여 NFT를 발행하였다.
5. Etherscan을 통해 스마트 컨트랙트와 민팅 트랜잭션을 확인하였다.
6. MetaMask에서 발행된 NFT를 확인하였다.

## 4. NFT 발행 결과

### 4.1. 스마트 컨트랙트

- Network: Ethereum Sepolia Testnet
- Contract Address: `0x9d05c8d92b3fc3d19cb8a586b4320e085371fe91`

[스마트 컨트랙트 Etherscan 링크](https://sepolia.etherscan.io/address/0x9d05c8d92b3fc3d19cb8a586b4320e085371fe91)

### 4.2. NFT 민팅 트랜잭션

[NFT 민팅 트랜잭션 Etherscan 링크](https://sepolia.etherscan.io/tx/0xf6c6362039c6159f05962160e2fe05d30968ddff6718b6076ed0ea91223ddfea)

### 4.3. 토큰 ID

`1`

### 4.4. 메타데이터 URI

`ipfs://bafkreifaltq2a3qmwavrvpjffhjdecw5fbkyzi65de2pbqqfexpsic5vie`

[NFT 메타데이터 확인](https://ipfs.io/ipfs/bafkreifaltq2a3qmwavrvpjffhjdecw5fbkyzi65de2pbqqfexpsic5vie)

## 5. 실습을 통해 배운 점

NFT가 단순히 이미지 파일을 블록체인에 저장하는 것이 아니라 스마트 컨트랙트에서 관리하는 토큰과 메타데이터를 연결하는 구조라는 것을 이해하였다.

또한 직접 스마트 컨트랙트를 배포하고 NFT를 발행하면서 블록체인에서 디지털 자산이 생성되고 관리되는 과정을 학습하였다.

Etherscan을 통해 스마트 컨트랙트의 주소와 트랜잭션 기록을 확인하는 방법도 익힐 수 있었다.
