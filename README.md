<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>EDEX 3D — Figuras Artesanales</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Cormorant+Garamond:ital,wght@0,300;0,400;0,600;1,300;1,400&family=Space+Mono:wght@400;700&family=Bebas+Neue&display=swap" rel="stylesheet">
<style>
    :root {
        --cream: #f5f0e8;
        --dark: #0d0b08;
        --amber: #c8832a;
        --amber-light: #e8a94a;
        --rust: #8b3a1a;
        --resin-blue: #1a3a5c;
        --resin-teal: #2a7a6a;
        --glass: rgba(245,240,232,0.06);
        --font-display: 'Bebas Neue', sans-serif;
        --font-serif: 'Cormorant Garamond', serif;
        --font-mono: 'Space Mono', monospace;
    }

  * { margin: 0; padding: 0; box-sizing: border-box; }

  html { scroll-behavior: smooth; overflow-x: hidden; }

  body {
    background: var(--dark);
    color: var(--cream);
    font-family: var(--font-serif);
    cursor: none;
    overflow-x: hidden;
  }

  /* CURSOR */
  #cursor {
    width: 12px; height: 12px;
    background: var(--amber);
    border-radius: 50%;
    position: fixed; top: 0; left: 0;
    pointer-events: none;
    z-index: 9999;
    transition: transform 0.1s ease;
    mix-blend-mode: difference;
  }
  #cursor-ring {
    width: 40px; height: 40px;
    border: 1px solid var(--amber);
    border-radius: 50%;
    position: fixed; top: 0; left: 0;
    pointer-events: none;
    z-index: 9998;
    transition: transform 0.15s ease, width 0.3s, height 0.3s, opacity 0.3s;
    opacity: 0.6;
  }

  /* WEBGL CANVAS */
  #webgl-bg {
    position: fixed;
    top: 0; left: 0;
    width: 100%; height: 100%;
    z-index: 0;
    pointer-events: none;
  }

  /* NAV */
  nav {
    position: fixed; top: 0; left: 0; right: 0;
    z-index: 100;
    display: flex; align-items: center; justify-content: space-between;
    padding: 24px 48px;
    background: linear-gradient(to bottom, rgba(13,11,8,0.9), transparent);
    backdrop-filter: blur(2px);
  }
  .nav-logo {
    font-family: var(--font-display);
    font-size: 28px;
    letter-spacing: 4px;
    color: var(--amber);
    text-decoration: none;
  }
  .nav-logo span { color: var(--cream); }
  .nav-links { display: flex; gap: 40px; }
  .nav-links a {
    font-family: var(--font-mono);
    font-size: 11px;
    letter-spacing: 2px;
    text-transform: uppercase;
    color: var(--cream);
    text-decoration: none;
    opacity: 0.7;
    transition: opacity 0.3s, color 0.3s;
  }
  .nav-links a:hover { opacity: 1; color: var(--amber); }
  .nav-cart {
    display: flex; align-items: center; gap: 8px;
    font-family: var(--font-mono);
    font-size: 11px;
    letter-spacing: 2px;
    color: var(--cream);
    background: none; border: 1px solid rgba(200,131,42,0.4);
    padding: 8px 20px; cursor: none;
    transition: all 0.3s;
    position: relative;
  }
  .nav-cart:hover { background: var(--amber); color: var(--dark); border-color: var(--amber); }
  .cart-count {
    background: var(--amber);
    color: var(--dark);
    border-radius: 50%;
    width: 18px; height: 18px;
    display: flex; align-items: center; justify-content: center;
    font-size: 10px; font-weight: 700;
  }

  /* HERO */
  #hero {
    position: relative; z-index: 1;
    height: 100vh;
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    text-align: center;
    overflow: hidden;
  }
  .hero-eyebrow {
    font-family: var(--font-mono);
    font-size: 11px; letter-spacing: 6px;
    text-transform: uppercase;
    color: var(--amber);
    margin-bottom: 24px;
    opacity: 0;
    animation: fadeUp 1s 0.3s forwards;
  }
  .hero-title {
    font-family: var(--font-display);
    font-size: clamp(72px, 14vw, 180px);
    line-height: 0.9;
    letter-spacing: 2px;
    background: linear-gradient(135deg, var(--cream) 0%, var(--amber-light) 40%, var(--amber) 70%, var(--rust) 100%);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    opacity: 0;
    animation: fadeUp 1s 0.5s forwards;
  }
  .hero-sub {
    font-family: var(--font-serif);
    /*font-style: italic;*/
    font-size: clamp(18px, 2.5vw, 28px);
    color: rgba(245,240,232,0.6);
    margin-top: 24px;
    opacity: 0;
    animation: fadeUp 1s 0.7s forwards;
  }
  .hero-cta {
    margin-top: 56px;
    display: flex; gap: 20px;
    opacity: 0;
    animation: fadeUp 1s 0.9s forwards;
  }
  .btn-primary {
    font-family: var(--font-mono);
    font-size: 11px; letter-spacing: 3px;
    text-transform: uppercase;
    background: var(--amber);
    color: var(--dark);
    border: none; padding: 18px 40px;
    cursor: none; text-decoration: none;
    transition: all 0.3s;
    display: inline-block;
    position: relative; overflow: hidden;
  }
  .btn-primary::after {
    content: '';
    position: absolute; inset: 0;
    background: white;
    transform: scaleX(0);
    transform-origin: left;
    transition: transform 0.4s ease;
    mix-blend-mode: overlay;
  }
  .btn-primary:hover::after { transform: scaleX(1); }
  .btn-secondary {
    font-family: var(--font-mono);
    font-size: 11px; letter-spacing: 3px;
    text-transform: uppercase;
    background: transparent;
    color: var(--cream);
    border: 1px solid rgba(245,240,232,0.3);
    padding: 18px 40px;
    cursor: none; text-decoration: none;
    transition: all 0.3s;
    display: inline-block;
  }
  .btn-secondary:hover { border-color: var(--amber); color: var(--amber); }
  .hero-scroll {
    position: absolute; bottom: 40px;
    display: flex; flex-direction: column; align-items: center; gap: 8px;
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 3px; color: rgba(245,240,232,0.4);
    opacity: 0; animation: fadeUp 1s 1.2s forwards;
  }
  .scroll-line {
    width: 1px; height: 60px;
    background: linear-gradient(to bottom, var(--amber), transparent);
    animation: scrollPulse 2s infinite;
  }

  /* SECTIONS */
  section { position: relative; z-index: 1; }

  /* MARQUEE */
  .marquee-wrap {
    overflow: hidden; border-top: 1px solid rgba(200,131,42,0.2);
    border-bottom: 1px solid rgba(200,131,42,0.2);
    padding: 16px 0; background: rgba(13,11,8,0.8);
  }
  .marquee {
    display: flex; gap: 60px;
    animation: marquee 20s linear infinite;
    white-space: nowrap;
  }
  .marquee span {
    font-family: var(--font-display);
    font-size: 14px; letter-spacing: 4px;
    color: rgba(200,131,42,0.5);
  }
  .marquee span.dot { color: var(--amber); }

  /* FEATURED PRODUCTS */
  #productos {
    padding: 120px 48px;
    background: linear-gradient(to bottom, rgba(13,11,8,0) 0%, rgba(13,11,8,0.95) 10%, rgba(13,11,8,0.95) 90%, rgba(13,11,8,0) 100%);
  }
  .section-header {
    display: flex; align-items: flex-end; justify-content: space-between;
    margin-bottom: 80px;
  }
  .section-eyebrow {
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 4px; text-transform: uppercase;
    color: var(--amber); margin-bottom: 12px;
  }
  .section-title {
    font-family: var(--font-display);
    font-size: clamp(48px, 6vw, 96px);
    line-height: 1; color: var(--cream);
  }
  .section-link {
    font-family: var(--font-mono); font-size: 11px;
    letter-spacing: 2px; text-transform: uppercase;
    color: var(--amber); text-decoration: none;
    border-bottom: 1px solid currentColor;
    padding-bottom: 2px;
    transition: opacity 0.3s;
  }
  .section-link:hover { opacity: 0.6; }

  /* PRODUCT GRID */
  .products-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 2px;
  }
    .product-card {
        position: relative;
        overflow: hidden;
        /*background: #111009;*/
        background: #000000;
        aspect-ratio: 3/4;
        cursor: none;
    }
  .product-card:first-child { aspect-ratio: auto; grid-row: span 2; }
  .product-img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
  }
  .product-card:hover .product-img {
    width: 100%;
    height: 100%;
    object-fit: cover;
    display: block;
  }
  .product-overlay {
    position: absolute; inset: 0;
    background: linear-gradient(to top, rgba(13,11,8,0.95) 0%, rgba(13,11,8,0.3) 50%, transparent 100%);
    display: flex; flex-direction: column; justify-content: flex-end;
    padding: 32px;
    transform: translateY(20px); opacity: 0;
    transition: all 0.5s cubic-bezier(0.25,0.46,0.45,0.94);
  }
  .product-card:hover .product-overlay { transform: translateY(0); opacity: 1; }
  .product-tag {
    font-family: var(--font-mono); font-size: 9px;
    letter-spacing: 3px; text-transform: uppercase;
    color: var(--amber); margin-bottom: 8px;
  }
  .product-name {
    font-family: var(--font-serif); font-size: 22px;
    font-style: italic; margin-bottom: 8px;
  }
  .product-price {
    font-family: var(--font-display); font-size: 28px;
    color: var(--amber-light);
  }
  .product-add {
    margin-top: 16px;
    display: flex; gap: 12px; align-items: center;
  }
  .btn-add {
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 2px; text-transform: uppercase;
    background: var(--amber); color: var(--dark);
    border: none; padding: 10px 20px; cursor: none;
    transition: all 0.3s;
  }
  .btn-add:hover { background: var(--amber-light); }
  .product-placeholder {
    width: 100%; height: 100%;
    display: flex; align-items: center; justify-content: center;
    font-size: 80px;
    background: radial-gradient(ellipse at center, #1a1208 0%, #0d0b08 100%);
    position: absolute; inset: 0;
  }

  /* PROCESS VIDEO SECTION */
  #proceso {
    padding: 120px 48px;
    display: grid; grid-template-columns: 1fr 1fr;
    gap: 80px; align-items: center;
  }
  .process-text .section-eyebrow { margin-bottom: 16px; }
  .process-text .section-title { margin-bottom: 32px; }
  .process-description {
    font-size: 18px; line-height: 1.8;
    color: rgba(245,240,232,0.7);
    margin-bottom: 40px;
  }
  .process-steps {
    display: flex; flex-direction: column; gap: 20px;
    margin-bottom: 48px;
  }
  .step {
    display: flex; gap: 20px; align-items: flex-start;
    padding-bottom: 20px;
    border-bottom: 1px solid rgba(200,131,42,0.15);
  }
  .step-num {
    font-family: var(--font-display); font-size: 48px;
    color: rgba(200,131,42,0.2); line-height: 1;
    min-width: 60px;
  }
  .step-content h4 {
    font-family: var(--font-mono); font-size: 11px;
    letter-spacing: 2px; text-transform: uppercase;
    color: var(--amber); margin-bottom: 6px;
  }
  .step-content p {
    font-size: 15px; color: rgba(245,240,232,0.6); line-height: 1.6;
  }
  .video-container {
    position: relative; aspect-ratio: 9/16;
    max-height: 700px; overflow: hidden;
  }
  .video-frame {
    width: 100%; height: 100%;
    object-fit: cover;
    border: 1px solid rgba(200,131,42,0.2);
  }
  .video-placeholder {
    width: 100%; height: 100%;
    background: linear-gradient(135deg, #1a1208 0%, #0d1a1a 50%, #1a0d08 100%);
    display: flex; flex-direction: column;
    align-items: center; justify-content: center;
    gap: 20px; border: 1px solid rgba(200,131,42,0.2);
    position: relative; overflow: hidden;
  }
  .video-placeholder::before {
    content: '';
    position: absolute; inset: 0;
    background: repeating-linear-gradient(
      45deg,
      transparent,
      transparent 20px,
      rgba(200,131,42,0.03) 20px,
      rgba(200,131,42,0.03) 21px
    );
  }
  .play-btn {
    width: 80px; height: 80px;
    border: 2px solid var(--amber);
    border-radius: 50%;
    display: flex; align-items: center; justify-content: center;
    cursor: none;
    transition: all 0.3s;
    position: relative; z-index: 1;
  }
  .play-btn::after {
    content: '▶';
    font-size: 28px; color: var(--amber);
    margin-left: 4px;
  }
  .play-btn:hover {
    background: var(--amber);
    transform: scale(1.1);
  }
  .play-btn:hover::after { color: var(--dark); }
  .video-label {
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 3px; text-transform: uppercase;
    color: rgba(245,240,232,0.5); position: relative; z-index: 1;
  }
  .video-badge {
    position: absolute; top: 20px; left: 20px;
    background: var(--amber); color: var(--dark);
    font-family: var(--font-mono); font-size: 9px;
    letter-spacing: 2px; text-transform: uppercase;
    padding: 6px 12px;
  }

  /* GALLERY STRIP */
  #galeria {
    padding: 80px 0;
    overflow: hidden;
  }
  .gallery-title {
    text-align: center; margin-bottom: 60px; padding: 0 48px;
  }
  .gallery-scroll {
    display: flex; gap: 16px;
    animation: galleryScroll 30s linear infinite;
    width: max-content;
  }
  .gallery-scroll:hover { animation-play-state: paused; }
  .gallery-item {
    width: 280px; height: 380px;
    flex-shrink: 0; position: relative;
    overflow: hidden; cursor: none;
  }
  .gallery-item-ph {
    width: 100%; height: 100%;
    display: flex; align-items: center; justify-content: center;
    font-size: 60px;
    background: linear-gradient(135deg, #1a1208, #0d1a1a);
    border: 1px solid rgba(200,131,42,0.1);
    transition: transform 0.5s ease;
  }
  .gallery-item:hover .gallery-item-ph { transform: scale(1.05); }

  /* TESTIMONIALS */
  #testimonios {
    padding: 120px 48px;
    background: rgba(200,131,42,0.04);
    border-top: 1px solid rgba(200,131,42,0.1);
    border-bottom: 1px solid rgba(200,131,42,0.1);
  }
  .testimonials-grid {
    display: grid; grid-template-columns: repeat(3, 1fr);
    gap: 40px; margin-top: 80px;
  }
  .testimonial {
    padding: 40px;
    border: 1px solid rgba(200,131,42,0.15);
    position: relative;
  }
  .testimonial::before {
    content: '"';
    font-family: var(--font-serif); font-size: 100px;
    color: rgba(200,131,42,0.1);
    position: absolute; top: -20px; left: 30px;
    line-height: 1;
  }
  .testimonial-text {
    font-style: italic; font-size: 17px;
    line-height: 1.7; color: rgba(245,240,232,0.8);
    margin-bottom: 24px; position: relative;
  }
  .testimonial-author {
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 2px; text-transform: uppercase;
    color: var(--amber);
  }
  .stars { color: var(--amber); margin-bottom: 16px; font-size: 14px; }

  /* CHECKOUT SECTION */
  #tienda {
    padding: 120px 48px;
  }
  .shop-layout {
    display: grid; grid-template-columns: 1fr 420px;
    gap: 80px; margin-top: 80px;
  }
  .product-list { display: flex; flex-direction: column; gap: 2px; }
  .product-row {
    display: grid; grid-template-columns: 100px 1fr auto auto;
    gap: 24px; align-items: center;
    padding: 24px;
    background: rgba(245,240,232,0.03);
    border: 1px solid rgba(200,131,42,0.1);
    transition: all 0.3s;
  }
  .product-row:hover { background: rgba(200,131,42,0.06); border-color: rgba(200,131,42,0.3); }
  .product-row-img {
    width: 100px; height: 100px;
    display: flex; align-items: center; justify-content: center;
    font-size: 40px;
    background: #111009;
    border: 1px solid rgba(200,131,42,0.1);
  }
  .product-row-name {
    font-family: var(--font-serif); font-style: italic;
    font-size: 20px; margin-bottom: 4px;
  }
  .product-row-sku {
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 2px; color: rgba(245,240,232,0.4);
  }
  .qty-control {
    display: flex; align-items: center; gap: 12px;
  }
  .qty-btn {
    width: 32px; height: 32px;
    background: transparent;
    border: 1px solid rgba(200,131,42,0.3);
    color: var(--cream); font-size: 18px;
    display: flex; align-items: center; justify-content: center;
    cursor: none; transition: all 0.3s;
  }
  .qty-btn:hover { background: var(--amber); border-color: var(--amber); color: var(--dark); }
  .qty-num {
    font-family: var(--font-mono); font-size: 14px;
    min-width: 24px; text-align: center;
  }
  .product-row-price {
    font-family: var(--font-display); font-size: 28px;
    color: var(--amber-light); text-align: right;
  }

  /* ORDER SUMMARY */
  .order-summary {
    background: rgba(245,240,232,0.03);
    border: 1px solid rgba(200,131,42,0.2);
    padding: 40px;
    position: sticky; top: 100px;
  }
  .summary-title {
    font-family: var(--font-display); font-size: 32px;
    letter-spacing: 2px; margin-bottom: 32px;
    padding-bottom: 20px; border-bottom: 1px solid rgba(200,131,42,0.2);
  }
  .summary-line {
    display: flex; justify-content: space-between;
    padding: 12px 0;
    font-size: 15px; color: rgba(245,240,232,0.7);
    border-bottom: 1px solid rgba(245,240,232,0.06);
  }
  .summary-line.total {
    font-family: var(--font-display); font-size: 28px;
    color: var(--cream); border-bottom: none;
    margin-top: 16px; padding-top: 20px;
    border-top: 1px solid rgba(200,131,42,0.3);
  }
  .summary-line.total span:last-child { color: var(--amber); }
  .form-group { margin: 24px 0 16px; }
  .form-label {
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 2px; text-transform: uppercase;
    color: rgba(245,240,232,0.5); display: block; margin-bottom: 8px;
  }
  .form-input {
    width: 100%; background: rgba(245,240,232,0.06);
    border: 1px solid rgba(200,131,42,0.2);
    color: var(--cream); padding: 12px 16px;
    font-family: var(--font-mono); font-size: 13px;
    transition: border-color 0.3s; outline: none;
  }
  .form-input:focus { border-color: var(--amber); }
  .form-input::placeholder { color: rgba(245,240,232,0.2); }
  .form-row { display: grid; grid-template-columns: 1fr 1fr; gap: 12px; }
  .shipping-options { margin: 24px 0; }
  .shipping-opt {
    display: flex; align-items: center; justify-content: space-between;
    padding: 14px 16px; margin-bottom: 8px;
    border: 1px solid rgba(200,131,42,0.15);
    cursor: none; transition: all 0.3s;
  }
  .shipping-opt.active { border-color: var(--amber); background: rgba(200,131,42,0.08); }
  .shipping-opt-left { display: flex; align-items: center; gap: 12px; }
  .shipping-radio {
    width: 16px; height: 16px; border-radius: 50%;
    border: 2px solid rgba(200,131,42,0.4);
    display: flex; align-items: center; justify-content: center;
    transition: all 0.3s;
  }
  .shipping-opt.active .shipping-radio { border-color: var(--amber); }
  .shipping-dot {
    width: 8px; height: 8px; border-radius: 50%;
    background: var(--amber); display: none;
  }
  .shipping-opt.active .shipping-dot { display: block; }
  .shipping-name {
    font-family: var(--font-mono); font-size: 11px;
    letter-spacing: 1px; text-transform: uppercase;
  }
  .shipping-time {
    font-size: 12px; color: rgba(245,240,232,0.4); margin-top: 2px;
  }
  .shipping-price {
    font-family: var(--font-display); font-size: 20px;
    color: var(--amber-light);
  }
  .btn-checkout {
    width: 100%; margin-top: 24px;
    font-family: var(--font-mono); font-size: 11px;
    letter-spacing: 3px; text-transform: uppercase;
    background: var(--amber); color: var(--dark);
    border: none; padding: 20px; cursor: none;
    transition: all 0.3s;
    position: relative; overflow: hidden;
  }
  .btn-checkout:hover { background: var(--amber-light); }
  .secure-note {
    text-align: center; margin-top: 16px;
    font-family: var(--font-mono); font-size: 9px;
    letter-spacing: 2px; text-transform: uppercase;
    color: rgba(245,240,232,0.3);
  }

  /* PAYMENT MODAL */
  .modal-overlay {
    display: none; position: fixed; inset: 0;
    background: rgba(13,11,8,0.95); z-index: 1000;
    align-items: center; justify-content: center;
  }
  .modal-overlay.active { display: flex; }
  .modal {
    background: #111009;
    border: 1px solid rgba(200,131,42,0.3);
    padding: 60px; max-width: 560px; width: 100%;
    position: relative;
    animation: modalIn 0.4s cubic-bezier(0.25,0.46,0.45,0.94) forwards;
  }
  .modal-close {
    position: absolute; top: 20px; right: 24px;
    background: none; border: none; color: var(--cream);
    font-size: 24px; cursor: none; opacity: 0.5;
    transition: opacity 0.3s;
  }
  .modal-close:hover { opacity: 1; }
  .modal-title {
    font-family: var(--font-display); font-size: 40px;
    letter-spacing: 2px; margin-bottom: 8px;
  }
  .modal-sub {
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 2px; text-transform: uppercase;
    color: var(--amber); margin-bottom: 40px;
  }
  .payment-tabs {
    display: flex; gap: 2px; margin-bottom: 32px;
  }
  .payment-tab {
    flex: 1; padding: 12px;
    background: rgba(245,240,232,0.04);
    border: 1px solid rgba(200,131,42,0.1);
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 2px; text-transform: uppercase;
    color: rgba(245,240,232,0.5); cursor: none;
    transition: all 0.3s;
  }
  .payment-tab.active {
    background: rgba(200,131,42,0.12);
    border-color: var(--amber); color: var(--amber);
  }
  .card-visual {
    background: linear-gradient(135deg, #1a2a1a 0%, #0d1a2a 50%, #2a1a0d 100%);
    border: 1px solid rgba(200,131,42,0.2);
    border-radius: 4px; padding: 28px;
    margin-bottom: 24px; position: relative; overflow: hidden;
  }
  .card-visual::before {
    content: '';
    position: absolute; top: -30px; right: -30px;
    width: 120px; height: 120px; border-radius: 50%;
    background: rgba(200,131,42,0.08);
  }
  .card-chip { font-size: 28px; margin-bottom: 20px; display: block; }
  .card-num-display {
    font-family: var(--font-mono); font-size: 18px;
    letter-spacing: 4px; color: rgba(245,240,232,0.8);
    margin-bottom: 16px;
  }
  .card-info-row {
    display: flex; justify-content: space-between;
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 2px; color: rgba(245,240,232,0.4);
  }
  .success-state {
    display: none; text-align: center; padding: 40px 0;
  }
  .success-icon { font-size: 64px; margin-bottom: 24px; display: block; }
  .success-title {
    font-family: var(--font-display); font-size: 48px;
    margin-bottom: 16px; color: var(--amber);
  }
  .success-msg {
    font-size: 18px; color: rgba(245,240,232,0.6);
    font-style: italic; line-height: 1.6;
  }
  .tracking-info {
    margin-top: 32px; padding: 20px;
    background: rgba(200,131,42,0.08);
    border: 1px solid rgba(200,131,42,0.2);
    font-family: var(--font-mono); font-size: 12px;
    letter-spacing: 2px;
  }
  .tracking-num { color: var(--amber); font-size: 20px; letter-spacing: 4px; }

  /* FOOTER */
  footer {
    padding: 80px 48px 40px;
    border-top: 1px solid rgba(200,131,42,0.15);
    background: rgba(13,11,8,0.98);
  }
  .footer-grid {
    display: grid; grid-template-columns: 2fr 1fr 1fr 1fr;
    gap: 60px; margin-bottom: 60px;
  }
  .footer-brand .nav-logo { font-size: 36px; display: block; margin-bottom: 16px; }
  .footer-desc {
    font-size: 15px; line-height: 1.7;
    color: rgba(245,240,232,0.5); max-width: 280px;
  }
  .footer-col h5 {
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 3px; text-transform: uppercase;
    color: var(--amber); margin-bottom: 24px;
  }
  .footer-links { display: flex; flex-direction: column; gap: 12px; }
  .footer-links a {
    font-size: 14px; color: rgba(245,240,232,0.5);
    text-decoration: none; transition: color 0.3s;
  }
  .footer-links a:hover { color: var(--amber); }
  .footer-bottom {
    display: flex; justify-content: space-between; align-items: center;
    padding-top: 40px; border-top: 1px solid rgba(245,240,232,0.06);
  }
  .footer-copy {
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 2px; color: rgba(245,240,232,0.3);
  }
  .footer-socials { display: flex; gap: 20px; }
  .social-link {
    font-family: var(--font-mono); font-size: 10px;
    letter-spacing: 2px; text-transform: uppercase;
    color: rgba(245,240,232,0.4); text-decoration: none;
    transition: color 0.3s;
  }
  .social-link:hover { color: var(--amber); }

  /* ANIMATIONS */
  @keyframes fadeUp {
    from { opacity: 0; transform: translateY(30px); }
    to { opacity: 1; transform: translateY(0); }
  }
  @keyframes scrollPulse {
    0%, 100% { opacity: 0.4; }
    50% { opacity: 1; }
  }
  @keyframes marquee {
    from { transform: translateX(0); }
    to { transform: translateX(-50%); }
  }
  @keyframes galleryScroll {
    from { transform: translateX(0); }
    to { transform: translateX(-50%); }
  }
  @keyframes modalIn {
    from { opacity: 0; transform: translateY(40px); }
    to { opacity: 1; transform: translateY(0); }
  }
  @keyframes float {
    0%, 100% { transform: translateY(0) rotate(0deg); }
    33% { transform: translateY(-20px) rotate(5deg); }
    66% { transform: translateY(-10px) rotate(-3deg); }
  }
  @keyframes pulse-ring {
    0% { transform: scale(1); opacity: 0.8; }
    100% { transform: scale(2); opacity: 0; }
  }

  .reveal {
    opacity: 0; transform: translateY(40px);
    transition: opacity 0.8s ease, transform 0.8s ease;
  }
  .reveal.visible { opacity: 1; transform: translateY(0); }

  /* NOTIFICATION */
  .notification {
    position: fixed; bottom: 32px; right: 32px;
    background: #111009; border: 1px solid var(--amber);
    padding: 20px 28px; z-index: 500;
    font-family: var(--font-mono); font-size: 11px;
    letter-spacing: 1px;
    transform: translateY(20px); opacity: 0;
    transition: all 0.4s cubic-bezier(0.25,0.46,0.45,0.94);
    display: flex; align-items: center; gap: 12px;
  }
  .notification.show { transform: translateY(0); opacity: 1; }
  .notification-icon { font-size: 20px; }

  @media (max-width: 1024px) {
    nav { padding: 20px 24px; }
    .nav-links { display: none; }
    #productos, #proceso, #galeria, #testimonios, #tienda, footer { padding-left: 24px; padding-right: 24px; }
    .products-grid { grid-template-columns: 1fr 1fr; }
    .product-card:first-child { grid-row: span 1; aspect-ratio: 3/4; }
    #proceso { grid-template-columns: 1fr; }
    .shop-layout { grid-template-columns: 1fr; }
    .testimonials-grid { grid-template-columns: 1fr; }
    .footer-grid { grid-template-columns: 1fr 1fr; }
    body { cursor: auto; }
    #cursor, #cursor-ring { display: none; }
  }

.image-viewer {
  position: fixed;
  inset: 0;
  background: rgba(13,11,8,0.95);
  display: none;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  z-index: 2000;
}

.image-viewer.active {
  display: flex;
}

#viewer-img {
  max-width: 90%;
  max-height: 80%;
  object-fit: contain;
  margin-bottom: 30px;
}

.viewer-close {
  position: absolute;
  top: 30px;
  right: 40px;
  font-size: 32px;
  cursor: pointer;
  color: var(--cream);
}

#viewer-add {
  cursor: none;
}


    .hero-logo {
        width: clamp(250px, 40vw, 520px);
        filter: drop-shadow(0 0 20px rgba(255,140,0,0.4)) drop-shadow(0 0 40px rgba(255,140,0,0.2));
        opacity: 0.95;
        mix-blend-mode: screen;
        animation: fadeUp 1s 0.5s forwards;
    }

    /* Ajustes individuales de imágenes */
    .contain {
        object-fit: contain !important;
    }

    .object-top {
        object-position: top;
    }

    .object-center {
        object-position: center;
    }

    .object-bottom {
        object-position: bottom;
    }

</style>
</head>
<body>

<!-- CUSTOM CURSOR -->
<div id="cursor"></div>
<div id="cursor-ring"></div>

<!-- WEBGL BACKGROUND -->
<canvas id="webgl-bg"></canvas>

<!-- NOTIFICATION -->
<div class="notification" id="notification">
  <span class="notification-icon">🛒</span>
  <span id="notification-text">Producto añadido al carrito</span>
</div>

<!-- NAV -->
<nav>
  <a href="#" class="nav-logo">EDEX<span>.3D</span></a>
  <div class="nav-links">
    <a href="#productos">Colección</a>
    <a href="#proceso">Proceso</a>
    <a href="#galeria">Galería</a>
    <a href="#tienda">Tienda</a>
  </div>
  <button class="nav-cart" onclick="openModal()">
    CARRITO
    <span class="cart-count" id="cart-count">0</span>
  </button>
</nav>

<!-- HERO -->
<section id="hero">
  <div class="hero-eyebrow">Artesanía Digital · Resina UV · Impresión 3D</div>
  
<img src="logo.png" class="hero-logo" alt="EDEX 3D Logo">

  <p class="hero-sub">Figuras artesanales creadas con tecnología<br>de impresión 3D en resina fotopolimérica</p>
  <div class="hero-cta">
    <a href="#productos" class="btn-primary">Ver Colección</a>
    <a href="#proceso" class="btn-secondary">Ver Proceso</a>
  </div>
  <div class="hero-scroll">
    <div class="scroll-line"></div>
    SCROLL
  </div>
</section>

<!-- MARQUEE -->
<div class="marquee-wrap">
  <div class="marquee">
    <span>RESINA UV</span><span class="dot">◆</span>
    <span>IMPRESIÓN 3D</span><span class="dot">◆</span>
    <span>FIGURAS ARTESANALES</span><span class="dot">◆</span>
    <span>ALTA DEFINICIÓN</span><span class="dot">◆</span>
    <span>PINTADO A MANO</span><span class="dot">◆</span>
    <span>EDICIÓN LIMITADA</span><span class="dot">◆</span>
    <span>RESINA UV</span><span class="dot">◆</span>
    <span>IMPRESIÓN 3D</span><span class="dot">◆</span>
    <span>FIGURAS ARTESANALES</span><span class="dot">◆</span>
    <span>ALTA DEFINICIÓN</span><span class="dot">◆</span>
    <span>PINTADO A MANO</span><span class="dot">◆</span>
    <span>EDICIÓN LIMITADA</span><span class="dot">◆</span>
  </div>
</div>

<!-- PRODUCTS -->
<section id="productos">
  <div class="section-header reveal">
    <div>
      <div class="section-eyebrow">Colección Principal</div>
      <h2 class="section-title">FIGURAS<br>DESTACADAS</h2>
    </div>
    <a href="#tienda" class="section-link">Ver todas →</a>
  </div>
  <div class="products-grid reveal">
      <!-- PRODUCTO: Dragón Ancestral — imágenes: frente y reverso de la figura -->
      <div class="product-card" onclick="addToCart('Dragón Ancestral', 2800)">
          <img src="okarun.png" class="product-img" onclick="openViewer('okarun.png','Dragón Ancestral',2800)" />
          <div class="product-overlay">
              <div class="product-tag">Edición Limitada · 12/50</div>
              <div class="product-name">Dragón Ancestral</div>
              <div class="product-price">RD$2,800</div>
              <div class="product-add">
                  <button class="btn-add">+ Añadir</button>
              </div>
          </div>
      </div>
      <div class="product-card" onclick="addToCart('Samurái del Viento', 1900)">
          <img src="samurai.png" class="product-img contain" onclick="openViewer('samurai.png','Samurái del Viento',1900)" />
          <div class="product-overlay">
              <div class="product-tag">Best Seller</div>
              <div class="product-name">Samurái del Viento</div>
              <div class="product-price">RD$1,900</div>
              <div class="product-add"><button class="btn-add">+ Añadir</button></div>
          </div>
      </div>
      <div class="product-card" onclick="addToCart('Kitsune Celestial', 2200)">
          <img src="geto.png" class="product-img contain" onclick="openViewer('geto.png','Kitsune Celestial',2200)" />
          <div class="product-overlay">
              <div class="product-tag">Nuevo</div>
              <div class="product-name">Kitsune Celestial</div>
              <div class="product-price">RD$2,200</div>
              <div class="product-add"><button class="btn-add">+ Añadir</button></div>
          </div>
      </div>
      <div class="product-card" onclick="addToCart('Guerrera de Cristal', 1600)">
          <img src="nobara.png" class="product-img" onclick="openViewer('nobara.png','Guerrera de Cristal',1600)" />
          <div class="product-overlay">
              <div class="product-tag">Popular</div>
              <div class="product-name">Guerrera de Cristal</div>
              <div class="product-price">RD$1,600</div>
              <div class="product-add"><button class="btn-add">+ Añadir</button></div>
          </div>
      </div>
      <div class="product-card" onclick="addToCart('Lobo Lunar', 3100)">
          <img src="jojo.png" class="product-img contain" onclick="openViewer('soporte_pc.png','Lobo Lunar',3100)" />
          <div class="product-overlay">
              <div class="product-tag">Premium · Ed. Especial</div>
              <div class="product-name">Lobo Lunar</div>
              <div class="product-price">RD$3,100</div>
              <div class="product-add"><button class="btn-add">+ Añadir</button></div>
          </div>
      </div>
  </div>
</section>

    <!--prueba de agregar una nueva seccion===========================================-->

<section id="productos">
    <div class="section-header reveal">
        <div>
            <div class="section-eyebrow">Colección Principal</div>
            <h2 class="section-title">MODELOS<br>RECIENTES</h2>
        </div>
        <a href="#tienda" class="section-link">Ver todas →</a>
    </div>
    <div class="products-grid reveal">
        <!-- PRODUCTO: Dragón Ancestral — imágenes: frente y reverso de la figura -->
        <div class="product-card" onclick="addToCart('Dragón Ancestral', 2800)">
            <img src="mesa.png" class="product-img contain" onclick="openViewer('okarun.png','Dragón Ancestral',2800)" />
            <div class="product-overlay">
                <div class="product-tag">Edición Limitada · 12/50</div>
                <div class="product-name">Dragón Ancestral</div>
                <div class="product-price">RD$2,800</div>
                <div class="product-add">
                    <button class="btn-add">+ Añadir</button>
                </div>
            </div>
        </div>
        <div class="product-card" onclick="addToCart('Samurái del Viento', 1900)">
            <img src="samurai.png" class="product-img" onclick="openViewer('samurai.png','Samurái del Viento',1900)" />
            <div class="product-overlay">
                <div class="product-tag">Best Seller</div>
                <div class="product-name">Samurái del Viento</div>
                <div class="product-price">RD$1,900</div>
                <div class="product-add"><button class="btn-add">+ Añadir</button></div>
            </div>
        </div>
        <div class="product-card" onclick="addToCart('Kitsune Celestial', 2200)">
            <img src="geto.png" class="product-img" onclick="openViewer('geto.png','Kitsune Celestial',2200)" />
            <div class="product-overlay">
                <div class="product-tag">Nuevo</div>
                <div class="product-name">Kitsune Celestial</div>
                <div class="product-price">RD$2,200</div>
                <div class="product-add"><button class="btn-add">+ Añadir</button></div>
            </div>
        </div>
        <div class="product-card" onclick="addToCart('Guerrera de Cristal', 1600)">
            <img src="nobara.png" class="product-img" onclick="openViewer('nobara.png','Guerrera de Cristal',1600)" />
            <div class="product-overlay">
                <div class="product-tag">Popular</div>
                <div class="product-name">Guerrera de Cristal</div>
                <div class="product-price">RD$1,600</div>
                <div class="product-add"><button class="btn-add">+ Añadir</button></div>
            </div>
        </div>
        <div class="product-card" onclick="addToCart('Lobo Lunar', 3100)">
            <img src="soporte_pc.png" class="product-img" onclick="openViewer('soporte_pc.png','Lobo Lunar',3100)" />
            <div class="product-overlay">
                <div class="product-tag">Premium · Ed. Especial</div>
                <div class="product-name">Lobo Lunar</div>
                <div class="product-price">RD$3,100</div>
                <div class="product-add"><button class="btn-add">+ Añadir</button></div>
            </div>
        </div>
    </div>
</section>

<!--=======prueba fin===============-->

<!-- PROCESS -->
<section id="proceso">
  <div class="process-text reveal">
    <div class="section-eyebrow">Nuestro Proceso</div>
    <h2 class="section-title">ARTE<br>EN RESINA</h2>
    <p class="process-description">
      Cada figura nace de un proceso meticuloso de diseño digital y fabricación artesanal. Usamos resina fotopolimérica de alta calidad curada con luz UV, logrando detalles imposibles con métodos tradicionales.
    </p>
    <div class="process-steps">
      <div class="step">
        <div class="step-num">01</div>
        <div class="step-content">
          <h4>Diseño 3D</h4>
          <p>Modelado digital en ZBrush y Blender con detalle micrométrico.</p>
        </div>
      </div>
      <div class="step">
        <div class="step-num">02</div>
        <div class="step-content">
          <h4>Impresión en Resina</h4>
          <p>Impresora de resina MSLA con resolución 8K para capturar cada detalle.</p>
        </div>
      </div>
      <div class="step">
        <div class="step-num">03</div>
        <div class="step-content">
          <h4>Curado UV</h4>
          <p>Curado con cámara UV de 405nm para máxima dureza y detalle.</p>
        </div>
      </div>
      <div class="step">
        <div class="step-num">04</div>
        <div class="step-content">
          <h4>Pintura a Mano</h4>
          <p>Aplicación manual de capas con aerógrafo y pincel de precisión.</p>
        </div>
      </div>
    </div>
    <a href="#galeria" class="btn-secondary">Ver Galería del Proceso →</a>
  </div>
  <video class="video-frame" autoplay muted loop playsinline>
      <source src="impresora.mp4" type="video/mp4">
</section>

<!-- GALLERY -->
<section id="galeria">
  <div class="gallery-title reveal">
    <div class="section-eyebrow">Galería</div>
    <h2 class="section-title">PROCESO<br>& DETALLE</h2>
  </div>
  <div style="overflow:hidden;">
    <div class="gallery-scroll">
      <div class="gallery-item"><div class="gallery-item-ph">🖨️</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">🎨</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">💎</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">🔬</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">✨</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">🐉</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">⚔️</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">🦊</div></div>
      <!-- Duplicado para loop -->
      <div class="gallery-item"><div class="gallery-item-ph">🖨️</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">🎨</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">💎</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">🔬</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">✨</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">🐉</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">⚔️</div></div>
      <div class="gallery-item"><div class="gallery-item-ph">🦊</div></div>
    </div>
  </div>
</section>

<!-- TESTIMONIALS -->
<section id="testimonios">
  <div class="reveal">
    <div class="section-eyebrow">Reseñas</div>
    <h2 class="section-title">LO QUE<br>DICEN</h2>
  </div>
  <div class="testimonials-grid reveal">
    <div class="testimonial">
      <div class="stars">★★★★★</div>
      <p class="testimonial-text">El detalle del Dragón Ancestral es absolutamente impresionante. Cada escama perfectamente definida. Una obra de arte que decora mi colección.</p>
      <div class="testimonial-author">— Carlos M., Santo Domingo</div>
    </div>
    <div class="testimonial">
      <div class="stars">★★★★★</div>
      <p class="testimonial-text">Llegó en perfectas condiciones, empaque protector y presentación de lujo. El Kitsune supera todas las expectativas, los colores son vibrantes y duraderos.</p>
      <div class="testimonial-author">— María L., Santiago</div>
    </div>
    <div class="testimonial">
      <div class="stars">★★★★★</div>
      <p class="testimonial-text">Increíble calidad de resina. He comprado en otras tiendas y ninguna tiene este nivel de precisión. EDEX 3D es mi única opción de ahora en adelante.</p>
      <div class="testimonial-author">— Javier R., San Pedro</div>
    </div>
  </div>
</section>

<!-- SHOP / CHECKOUT -->
<section id="tienda">
  <div class="reveal">
    <div class="section-eyebrow">Tienda</div>
    <h2 class="section-title">COMPRA<br>AHORA</h2>
  </div>
  <div class="shop-layout">
    <div>
      <div class="product-list" id="product-list">
        <div class="product-row">
            <!--modificacion de imagen-->
            <img src="Captura de pantalla 2026-04-04 213959" alt="Dragón Ancestral" style="width:100px; height:100px; object-fit:cover;">
          <div>
            <div class="product-row-name">Dragón Ancestral</div>
            <div class="product-row-sku">SKU-001 · 18cm · Resina UV Premium</div>
          </div>
          <div class="qty-control">
            <button class="qty-btn" onclick="changeQty('dragon', -1)">−</button>
            <span class="qty-num" id="qty-dragon">1</span>
            <button class="qty-btn" onclick="changeQty('dragon', 1)">+</button>
          </div>
          <div class="product-row-price" id="price-dragon">RD$2,800</div>
        </div>
        <div class="product-row">
          <div class="product-row-img">⚔️</div>
          <div>
            <div class="product-row-name">Samurái del Viento</div>
            <div class="product-row-sku">SKU-002 · 15cm · Resina UV Premium</div>
          </div>
          <div class="qty-control">
            <button class="qty-btn" onclick="changeQty('samurai', -1)">−</button>
            <span class="qty-num" id="qty-samurai">0</span>
            <button class="qty-btn" onclick="changeQty('samurai', 1)">+</button>
          </div>
          <div class="product-row-price" id="price-samurai">RD$0</div>
        </div>
        <div class="product-row">
          <div class="product-row-img">🦊</div>
          <div>
            <div class="product-row-name">Kitsune Celestial</div>
            <div class="product-row-sku">SKU-003 · 14cm · Resina UV Premium</div>
          </div>
          <div class="qty-control">
            <button class="qty-btn" onclick="changeQty('kitsune', -1)">−</button>
            <span class="qty-num" id="qty-kitsune">0</span>
            <button class="qty-btn" onclick="changeQty('kitsune', 1)">+</button>
          </div>
          <div class="product-row-price" id="price-kitsune">RD$0</div>
        </div>
        <div class="product-row">
          <div class="product-row-img">🐺</div>
          <div>
            <div class="product-row-name">Lobo Lunar</div>
            <div class="product-row-sku">SKU-004 · 22cm · Edición Especial</div>
          </div>
          <div class="qty-control">
            <button class="qty-btn" onclick="changeQty('lobo', -1)">−</button>
            <span class="qty-num" id="qty-lobo">0</span>
            <button class="qty-btn" onclick="changeQty('lobo', 1)">+</button>
          </div>
          <div class="product-row-price" id="price-lobo">RD$0</div>
        </div>
      </div>
    </div>

    <!-- ORDER SUMMARY -->
    <div class="order-summary">
      <div class="summary-title">RESUMEN</div>

      <div class="summary-line">
        <span>Subtotal</span>
        <span id="subtotal-display">RD$2,800</span>
      </div>

      <div class="shipping-options">
        <div style="font-family:var(--font-mono);font-size:10px;letter-spacing:2px;text-transform:uppercase;color:rgba(245,240,232,0.5);margin-bottom:12px;">Envío</div>
        <div class="shipping-opt active" onclick="selectShipping(this, 200, 'Estándar')">
          <div class="shipping-opt-left">
            <div class="shipping-radio"><div class="shipping-dot"></div></div>
            <div>
              <div class="shipping-name">Estándar Nacional</div>
              <div class="shipping-time">5-7 días hábiles</div>
            </div>
          </div>
          <div class="shipping-price">RD$200</div>
        </div>
        <div class="shipping-opt" onclick="selectShipping(this, 450, 'Express')">
          <div class="shipping-opt-left">
            <div class="shipping-radio"><div class="shipping-dot"></div></div>
            <div>
              <div class="shipping-name">Express</div>
              <div class="shipping-time">2-3 días hábiles</div>
            </div>
          </div>
          <div class="shipping-price">RD$450</div>
        </div>
        <div class="shipping-opt" onclick="selectShipping(this, 0, 'Recogida')">
          <div class="shipping-opt-left">
            <div class="shipping-radio"><div class="shipping-dot"></div></div>
            <div>
              <div class="shipping-name">Recoger en tienda</div>
              <div class="shipping-time">Santo Domingo · Hoy</div>
            </div>
          </div>
          <div class="shipping-price">GRATIS</div>
        </div>
      </div>

      <div class="summary-line">
        <span>Envío</span>
        <span id="shipping-display">RD$200</span>
      </div>
      <div class="summary-line">
        <span>ITBIS (18%)</span>
        <span id="tax-display">RD$504</span>
      </div>
      <div class="summary-line total">
        <span>TOTAL</span>
        <span id="total-display">RD$3,504</span>
      </div>

      <div class="form-group">
        <label class="form-label">Nombre completo</label>
        <input type="text" class="form-input" placeholder="Juan Pérez" id="customer-name">
      </div>
      <div class="form-group">
        <label class="form-label">Email</label>
        <input type="email" class="form-input" placeholder="tu@email.com" id="customer-email">
      </div>
      <div class="form-group">
        <label class="form-label">Dirección de envío</label>
        <input type="text" class="form-input" placeholder="Calle, No., Ciudad" id="customer-address">
      </div>

      <button class="btn-checkout" onclick="openModal()">
        PROCEDER AL PAGO →
      </button>
      <div class="secure-note">🔒 Pago seguro 256-bit SSL encriptado</div>
    </div>
  </div>
</section>

<!-- FOOTER -->
<footer>
  <div class="footer-grid">
    <div class="footer-brand">
      <a href="#" class="nav-logo">EDEX<span>.3D</span></a>
      <p class="footer-desc">Figuras artesanales creadas con pasión, tecnología de vanguardia y resina fotopolimérica de máxima calidad. República Dominicana.</p>
    </div>
    <div class="footer-col">
      <h5>Tienda</h5>
      <div class="footer-links">
        <a href="#productos">Colección</a>
        <a href="#tienda">Comprar</a>
        <a href="#">Personalizar</a>
        <a href="#">Ediciones Limitadas</a>
      </div>
    </div>
    <div class="footer-col">
      <h5>Info</h5>
      <div class="footer-links">
        <a href="#proceso">Nuestro Proceso</a>
        <a href="#">Materiales</a>
        <a href="#">Política de envíos</a>
        <a href="#">Devoluciones</a>
      </div>
    </div>
    <div class="footer-col">
      <h5>Contacto</h5>
      <div class="footer-links">
        <a href="#">info@EDEX 3D.do</a>
        <a href="#">+1 (809) 000-0000</a>
        <a href="#">Santo Domingo, RD</a>
        <a href="#">WhatsApp</a>
      </div>
    </div>
  </div>
  <div class="footer-bottom">
    <div class="footer-copy">© 2025 EDEX 3D — Todos los derechos reservados</div>
    <div class="footer-socials">
      <a href="#" class="social-link">Instagram</a>
      <a href="#" class="social-link">TikTok</a>
      <a href="#" class="social-link">Facebook</a>
    </div>
  </div>
</footer>

<!-- PAYMENT MODAL -->
<div class="modal-overlay" id="modal" onclick="closeModalOutside(event)">
  <div class="modal">
    <button class="modal-close" onclick="closeModal()">✕</button>

    <div id="payment-form-state">
      <div class="modal-title">PAGO</div>
      <div class="modal-sub">Simulación de pago seguro</div>

      <div class="payment-tabs">
        <button class="payment-tab active" onclick="switchTab(this, 'card')">💳 Tarjeta</button>
        <button class="payment-tab" onclick="switchTab(this, 'transfer')">🏦 Transferencia</button>
        <button class="payment-tab" onclick="switchTab(this, 'cash')">💵 Efectivo</button>
      </div>

      <div id="tab-card">
        <div class="card-visual">
          <span class="card-chip">💳</span>
          <div class="card-num-display" id="card-display">•••• •••• •••• ••••</div>
          <div class="card-info-row">
            <span id="card-name-display">NOMBRE TITULAR</span>
            <span id="card-exp-display">MM/AA</span>
          </div>
        </div>
        <div class="form-group">
          <label class="form-label">Número de tarjeta</label>
          <input type="text" class="form-input" placeholder="1234 5678 9012 3456" maxlength="19"
            oninput="formatCard(this)" id="card-num">
        </div>
        <div class="form-row">
          <div>
            <label class="form-label">Titular</label>
            <input type="text" class="form-input" placeholder="NOMBRE APELLIDO" id="card-holder"
              oninput="document.getElementById('card-name-display').textContent=this.value||'NOMBRE TITULAR'">
          </div>
          <div>
            <label class="form-label">Vencimiento</label>
            <input type="text" class="form-input" placeholder="MM/AA" maxlength="5"
              oninput="formatExp(this)" id="card-exp">
          </div>
        </div>
        <div class="form-group">
          <label class="form-label">CVV</label>
          <input type="text" class="form-input" placeholder="•••" maxlength="3" id="card-cvv">
        </div>
      </div>

      <div id="tab-transfer" style="display:none;">
        <div style="padding:32px;background:rgba(200,131,42,0.06);border:1px solid rgba(200,131,42,0.2);margin-bottom:24px;">
          <div style="font-family:var(--font-mono);font-size:10px;letter-spacing:2px;color:var(--amber);margin-bottom:16px;">DATOS BANCARIOS</div>
          <div style="font-size:16px;line-height:2;color:rgba(245,240,232,0.8);">
            <strong>Banco:</strong> Banreservas<br>
            <strong>Cuenta:</strong> 000-123456-7<br>
            <strong>A nombre de:</strong> RESINA 3D SRL<br>
            <strong>Concepto:</strong> Tu número de orden
          </div>
        </div>
        <p style="font-size:14px;color:rgba(245,240,232,0.5);line-height:1.6;">Envía el comprobante a <span style="color:var(--amber);">pagos@EDEX 3D.do</span> y procesaremos tu pedido en 24h.</p>
      </div>

      <div id="tab-cash" style="display:none;">
        <div style="padding:32px;background:rgba(200,131,42,0.06);border:1px solid rgba(200,131,42,0.2);margin-bottom:24px;">
          <div style="font-family:var(--font-mono);font-size:10px;letter-spacing:2px;color:var(--amber);margin-bottom:16px;">PAGO EN EFECTIVO</div>
          <div style="font-size:16px;line-height:2;color:rgba(245,240,232,0.8);">
            📍 <strong>Dirección:</strong> Calle El Vergel #45, Naco<br>
            ⏰ <strong>Horario:</strong> Lun-Sáb 9am - 7pm<br>
            📞 <strong>Cita previa:</strong> +1 (809) 000-0000
          </div>
        </div>
        <p style="font-size:14px;color:rgba(245,240,232,0.5);line-height:1.6;">También aceptamos pagos contra entrega en Santo Domingo capital con un cargo adicional de RD$150.</p>
      </div>

      <div style="display:flex;justify-content:space-between;align-items:center;margin-top:24px;padding:16px;background:rgba(200,131,42,0.06);">
        <span style="font-family:var(--font-mono);font-size:11px;color:rgba(245,240,232,0.5);">TOTAL A PAGAR</span>
        <span style="font-family:var(--font-display);font-size:32px;color:var(--amber);" id="modal-total">RD$3,504</span>
      </div>

      <button class="btn-checkout" onclick="processPayment()" style="margin-top:16px;">
        CONFIRMAR PAGO →
      </button>
    </div>

    <div class="success-state" id="success-state">
      <span class="success-icon">✅</span>
      <div class="success-title">¡CONFIRMADO!</div>
      <p class="success-msg">Tu pedido ha sido procesado exitosamente. Recibirás un email de confirmación con los detalles de tu envío.</p>
      <div class="tracking-info">
        <div style="font-family:var(--font-mono);font-size:10px;letter-spacing:2px;color:rgba(245,240,232,0.5);margin-bottom:8px;">NÚMERO DE SEGUIMIENTO</div>
        <div class="tracking-num" id="tracking-num">RD3D-000000</div>
        <div style="margin-top:12px;font-size:13px;color:rgba(245,240,232,0.5);">Tiempo estimado de entrega: <span style="color:var(--amber);">5-7 días hábiles</span></div>
      </div>
      <button class="btn-primary" onclick="closeModal()" style="margin-top:32px;cursor:none;">SEGUIR COMPRANDO</button>
    </div>
  </div>
</div>

<script>
// ═══════════════════════════════════════════
// WEBGL BACKGROUND — Partículas flotantes
// ═══════════════════════════════════════════
const canvas = document.getElementById('webgl-bg');
const gl = canvas.getContext('webgl') || canvas.getContext('experimental-webgl');

let animFrame;
if (gl) {
  const vsSource = `
    attribute vec2 a_position;
    attribute float a_size;
    attribute float a_alpha;
    uniform vec2 u_resolution;
    uniform float u_time;
    varying float v_alpha;
    void main() {
      vec2 pos = a_position;
      pos.y = mod(pos.y + u_time * 0.00015 * (0.5 + a_alpha), 1.0);
      pos.x += sin(u_time * 0.0008 + a_alpha * 6.28) * 0.02;
      vec2 clipSpace = pos * 2.0 - 1.0;
      gl_Position = vec4(clipSpace * vec2(1, -1), 0, 1);
      gl_PointSize = a_size;
      v_alpha = a_alpha;
    }
  `;
  const fsSource = `
    precision mediump float;
    varying float v_alpha;
    void main() {
      vec2 uv = gl_PointCoord - 0.5;
      float dist = length(uv);
      if (dist > 0.5) discard;
      float alpha = smoothstep(0.5, 0.1, dist) * v_alpha * 0.7;
      vec3 color1 = vec3(1.0, 0.55, 0.0);   // naranja vivo
      vec3 color2 = vec3(1.0, 0.35, 0.0);   // naranja oscuro
      vec3 color = mix(color1, color2, v_alpha);
      gl_FragColor = vec4(color, alpha);
    }
  `;

  function createShader(gl, type, src) {
    const s = gl.createShader(type);
    gl.shaderSource(s, src);
    gl.compileShader(s);
    return s;
  }

  const vs = createShader(gl, gl.VERTEX_SHADER, vsSource);
  const fs = createShader(gl, gl.FRAGMENT_SHADER, fsSource);
  const prog = gl.createProgram();
  gl.attachShader(prog, vs);
  gl.attachShader(prog, fs);
  gl.linkProgram(prog);
  gl.useProgram(prog);

  const N = 180;
  const pos = new Float32Array(N * 2);
  const sizes = new Float32Array(N);
  const alphas = new Float32Array(N);

  for (let i = 0; i < N; i++) {
    pos[i*2] = Math.random();
    pos[i*2+1] = Math.random();
    sizes[i] = 1.5 + Math.random() * 4;
    alphas[i] = Math.random();
  }

  const posBuf = gl.createBuffer();
  gl.bindBuffer(gl.ARRAY_BUFFER, posBuf);
  gl.bufferData(gl.ARRAY_BUFFER, pos, gl.DYNAMIC_DRAW);

  const sizeBuf = gl.createBuffer();
  gl.bindBuffer(gl.ARRAY_BUFFER, sizeBuf);
  gl.bufferData(gl.ARRAY_BUFFER, sizes, gl.STATIC_DRAW);

  const alphaBuf = gl.createBuffer();
  gl.bindBuffer(gl.ARRAY_BUFFER, alphaBuf);
  gl.bufferData(gl.ARRAY_BUFFER, alphas, gl.STATIC_DRAW);

  const aPosLoc = gl.getAttribLocation(prog, 'a_position');
  const aSizeLoc = gl.getAttribLocation(prog, 'a_size');
  const aAlphaLoc = gl.getAttribLocation(prog, 'a_alpha');
  const uTimeLoc = gl.getUniformLocation(prog, 'u_time');
  const uResLoc = gl.getUniformLocation(prog, 'u_resolution');

  gl.enable(gl.BLEND);
  gl.blendFunc(gl.SRC_ALPHA, gl.ONE_MINUS_SRC_ALPHA);

  function resize() {
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;
    gl.viewport(0, 0, canvas.width, canvas.height);
  }
  resize();
  window.addEventListener('resize', resize);

  function render(t) {
    gl.clearColor(0, 0, 0, 0);
    gl.clear(gl.COLOR_BUFFER_BIT);
    gl.uniform1f(uTimeLoc, t);
    gl.uniform2f(uResLoc, canvas.width, canvas.height);

    gl.bindBuffer(gl.ARRAY_BUFFER, posBuf);
    gl.enableVertexAttribArray(aPosLoc);
    gl.vertexAttribPointer(aPosLoc, 2, gl.FLOAT, false, 0, 0);

    gl.bindBuffer(gl.ARRAY_BUFFER, sizeBuf);
    gl.enableVertexAttribArray(aSizeLoc);
    gl.vertexAttribPointer(aSizeLoc, 1, gl.FLOAT, false, 0, 0);

    gl.bindBuffer(gl.ARRAY_BUFFER, alphaBuf);
    gl.enableVertexAttribArray(aAlphaLoc);
    gl.vertexAttribPointer(aAlphaLoc, 1, gl.FLOAT, false, 0, 0);

    gl.drawArrays(gl.POINTS, 0, N);
    animFrame = requestAnimationFrame(render);
  }
  animFrame = requestAnimationFrame(render);
}

// ═══════════════════════════════════════════
// CURSOR
// ═══════════════════════════════════════════
const cursor = document.getElementById('cursor');
const ring = document.getElementById('cursor-ring');
let mx = 0, my = 0, rx = 0, ry = 0;

document.addEventListener('mousemove', e => {
  mx = e.clientX; my = e.clientY;
  cursor.style.transform = `translate(${mx-6}px, ${my-6}px)`;
});

function animRing() {
  rx += (mx - rx) * 0.12;
  ry += (my - ry) * 0.12;
  ring.style.transform = `translate(${rx-20}px, ${ry-20}px)`;
  requestAnimationFrame(animRing);
}
animRing();

document.querySelectorAll('a, button, .product-card, .gallery-item, .shipping-opt').forEach(el => {
  el.addEventListener('mouseenter', () => {
    ring.style.width = '60px'; ring.style.height = '60px';
    ring.style.opacity = '1';
  });
  el.addEventListener('mouseleave', () => {
    ring.style.width = '40px'; ring.style.height = '40px';
    ring.style.opacity = '0.6';
  });
});

// ═══════════════════════════════════════════
// REVEAL ON SCROLL
// ═══════════════════════════════════════════
const revealEls = document.querySelectorAll('.reveal');
const observer = new IntersectionObserver((entries) => {
  entries.forEach(e => {
    if (e.isIntersecting) e.target.classList.add('visible');
  });
}, { threshold: 0.1 });
revealEls.forEach(el => observer.observe(el));

// ═══════════════════════════════════════════
// CART / QUANTITIES
// ═══════════════════════════════════════════
const prices = { dragon: 2800, samurai: 1900, kitsune: 2200, lobo: 3100 };
const qtys = { dragon: 1, samurai: 0, kitsune: 0, lobo: 0 };
let shippingCost = 200;
let cartCount = 0;

function changeQty(product, delta) {
  qtys[product] = Math.max(0, qtys[product] + delta);
  document.getElementById('qty-' + product).textContent = qtys[product];
  const total = prices[product] * qtys[product];
  document.getElementById('price-' + product).textContent = 'RD$' + total.toLocaleString();
  updateSummary();
}

function updateSummary() {
  const sub = Object.keys(qtys).reduce((a, k) => a + prices[k] * qtys[k], 0);
  const tax = Math.round(sub * 0.18);
  const total = sub + tax + shippingCost;
  document.getElementById('subtotal-display').textContent = 'RD$' + sub.toLocaleString();
  document.getElementById('shipping-display').textContent = shippingCost === 0 ? 'GRATIS' : 'RD$' + shippingCost.toLocaleString();
  document.getElementById('tax-display').textContent = 'RD$' + tax.toLocaleString();
  document.getElementById('total-display').textContent = 'RD$' + total.toLocaleString();
  document.getElementById('modal-total').textContent = 'RD$' + total.toLocaleString();

  cartCount = Object.values(qtys).reduce((a, b) => a + b, 0);
  document.getElementById('cart-count').textContent = cartCount;
}

function selectShipping(el, cost, name) {
  document.querySelectorAll('.shipping-opt').forEach(o => o.classList.remove('active'));
  el.classList.add('active');
  shippingCost = cost;
  updateSummary();
}

function addToCart(name, price) {
  const key = Object.keys(prices).find(k => prices[k] === price) || 'dragon';
  qtys[key] = (qtys[key] || 0) + 1;
  document.getElementById('qty-' + key).textContent = qtys[key];
  document.getElementById('price-' + key).textContent = 'RD$' + (prices[key] * qtys[key]).toLocaleString();
  updateSummary();
  showNotification('¡' + name + ' añadido al carrito!');
}

// ═══════════════════════════════════════════
// NOTIFICATION
// ═══════════════════════════════════════════
let notifTimer;
function showNotification(msg) {
  const n = document.getElementById('notification');
  document.getElementById('notification-text').textContent = msg;
  n.classList.add('show');
  clearTimeout(notifTimer);
  notifTimer = setTimeout(() => n.classList.remove('show'), 3000);
}

// ═══════════════════════════════════════════
// MODAL
// ═══════════════════════════════════════════
function openModal() {
  document.getElementById('modal').classList.add('active');
  document.body.style.overflow = 'hidden';
  document.getElementById('payment-form-state').style.display = 'block';
  document.getElementById('success-state').style.display = 'none';
}
function closeModal() {
  document.getElementById('modal').classList.remove('active');
  document.body.style.overflow = '';
}
function closeModalOutside(e) {
  if (e.target === document.getElementById('modal')) closeModal();
}

function switchTab(btn, tab) {
  document.querySelectorAll('.payment-tab').forEach(t => t.classList.remove('active'));
  btn.classList.add('active');
  document.getElementById('tab-card').style.display = tab === 'card' ? 'block' : 'none';
  document.getElementById('tab-transfer').style.display = tab === 'transfer' ? 'block' : 'none';
  document.getElementById('tab-cash').style.display = tab === 'cash' ? 'block' : 'none';
}

function formatCard(input) {
  let v = input.value.replace(/\D/g, '').substring(0, 16);
  input.value = v.replace(/(.{4})/g, '$1 ').trim();
  const disp = v.padEnd(16, '•');
  document.getElementById('card-display').textContent =
    disp.substring(0,4) + ' ' + disp.substring(4,8) + ' ' + disp.substring(8,12) + ' ' + disp.substring(12,16);
}

function formatExp(input) {
  let v = input.value.replace(/\D/g, '').substring(0, 4);
  if (v.length >= 2) v = v.substring(0,2) + '/' + v.substring(2);
  input.value = v;
  document.getElementById('card-exp-display').textContent = v || 'MM/AA';
}

function processPayment() {
  const btn = document.querySelector('.btn-checkout');
  btn.textContent = 'PROCESANDO...';
  btn.style.opacity = '0.7';
  setTimeout(() => {
    const trackNum = 'RD3D-' + Math.floor(100000 + Math.random() * 900000);
    document.getElementById('tracking-num').textContent = trackNum;
    document.getElementById('payment-form-state').style.display = 'none';
    document.getElementById('success-state').style.display = 'block';
    // Reset cart
    Object.keys(qtys).forEach(k => { qtys[k] = 0; });
    document.getElementById('qty-dragon').textContent = '0';
    document.getElementById('qty-samurai').textContent = '0';
    document.getElementById('qty-kitsune').textContent = '0';
    document.getElementById('qty-lobo').textContent = '0';
    Object.keys(prices).forEach(k => document.getElementById('price-' + k).textContent = 'RD$0');
    updateSummary();
    btn.textContent = 'CONFIRMAR PAGO →';
    btn.style.opacity = '1';
  }, 2200);
}

function playVideo(el) {
  el.innerHTML = '<div style="font-family:var(--font-mono);font-size:12px;letter-spacing:2px;color:var(--amber);text-align:center;padding:40px;">▶ REPRODUCIENDO VIDEO<br><br><span style="color:rgba(245,240,232,0.4);font-size:10px;">Reemplaza este div con tu etiqueta &lt;video&gt;</span></div>';
}

// Init
updateSummary();

let currentProduct = null;

function openViewer(img, name, price) {
  const viewer = document.getElementById('image-viewer');
  const viewerImg = document.getElementById('viewer-img');
  const addBtn = document.getElementById('viewer-add');

  viewerImg.src = img;
  viewer.classList.add('active');

  currentProduct = { name, price };

  addBtn.onclick = () => {
    addToCart(name, price);
    closeViewer();
  };
}

function closeViewer() {
  document.getElementById('image-viewer').classList.remove('active');
}

</script>

<div id="image-viewer" class="image-viewer">
  <span class="viewer-close" onclick="closeViewer()">✕</span>
  <img id="viewer-img" src="" alt="Producto">
  <button id="viewer-add" class="btn-primary">Añadir al carrito</button>
</div>

</body>
</html>
