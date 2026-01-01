---
title: "API Gateway Service"
description: "A high-performance API gateway built with Go, featuring rate limiting, authentication, and request routing."
date: 2024-02-20
tags: ["Go", "API", "Microservices", "Redis"]
github: "https://github.com/beingadityak/api-gateway"
---

## Overview

A lightweight, high-performance API gateway designed for microservices architectures. Built with Go for maximum performance and minimal resource usage.

## Features

- **Rate Limiting** - Configurable rate limiting with Redis backend
- **Authentication** - JWT and API key authentication support
- **Load Balancing** - Round-robin and weighted load balancing
- **Circuit Breaker** - Automatic circuit breaking for failing services
- **Metrics** - Prometheus metrics for monitoring

## Architecture

The gateway uses a plugin-based architecture that allows for easy extension. Core functionality is handled by the main router, while features like authentication and rate limiting are implemented as middleware plugins.

## Performance

Benchmarks show the gateway can handle over 50,000 requests per second on modest hardware, with sub-millisecond latency for most requests.
