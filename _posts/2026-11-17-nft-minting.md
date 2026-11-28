---
title: "Ethereum 테스트넷을 활용한 NFT 발행 실습"
date: 2026-11-17 12:00:00 +0900
categories: ["블로그/기술문서"]
tags: [Blockchain, Ethereum, NFT, ERC721, IPFS, Sepolia]
---

## 1. 실습 개요

블록체인 응용 과목에서 NFT의 구조와 동작 원리를 학습하고 이를 바탕으로 Ethereum 테스트넷에서 NFT를 직접 발행하는 실습을 진행하였다.

## 2. 실습 도구

- [Pinata](https://pinata.cloud/)
- [IPFS](https://ipfs.tech/)
- [Remix IDE](https://remix.ethereum.org/)
- [MetaMask](https://metamask.io/)
- Sepolia Testnet
- [Sepolia Etherscan](https://sepolia.etherscan.io/)

## 3. NFT 발행 과정

### 3.1. 이미지 및 메타데이터

Pinata를 활용하여 NFT에 사용할 이미지를 IPFS에 업로드하였다.

NFT의 이름, 설명, 이미지 주소 등의 정보는 JSON 형식의 메타데이터를 통해 관리할 수 있다.

### 3.2. 스마트 컨트랙트 배포

Remix IDE를 활용하여 ERC-721 표준을 기반으로 하는 NFT 스마트 컨트랙트를 Sepolia 테스트넷에 배포하는 과정을 실습하였다.

### 3.3. NFT 민팅

스마트 컨트랙트의 민팅 기능을 활용하여 NFT를 발행하는 과정을 진행하였다.

NFT 발행 결과는 토큰 ID, 지갑 주소, 메타데이터 URI 및 트랜잭션 정보를 통해 확인할 수 있다.

## 4. NFT 발행 결과

- Network: Ethereum Sepolia Testnet
- Token ID: 1
- Contract Address: `0x9d05c8d92b3fc3d19cb8a586b4320e085371fe91`

### 실습 결과 확인

- [NFT 스마트 컨트랙트](https://sepolia.etherscan.io/address/0x9d05c8d92b3fc3d19cb8a586b4320e085371fe91)
- [NFT 민팅 트랜잭션](https://sepolia.etherscan.io/tx/0xf6c6362039c6159f05962160e2fe05d30968ddff6718b6076ed0ea91223ddfea)
- [NFT 메타데이터](https://ipfs.io/ipfs/bafkreifaltq2a3qmwavrvpjffhjdecw5fbkyzi65de2pbqqfexpsic5vie)

## 5. 실습을 통해 배운 점

NFT가 단순히 이미지 파일을 블록체인에 저장하는 방식이 아니라 토큰과 메타데이터를 연결하여 관리하는 구조라는 것을 이해하였다.

또한 스마트 컨트랙트 배포와 NFT 발행 과정을 실습하면서 블록체인에서 디지털 자산이 생성되고 관리되는 방식을 학습하였다.
