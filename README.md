# Sarina
Game
import React, { useEffect, useRef, useState } from "react"; import { Card, CardContent } from "@/components/ui/card"; import { Button } from "@/components/ui/button"; import { motion } from "framer-motion";

// Simple Free Fire–style top-down mini battle royale (single player vs bots) // Arrow keys / WASD = move // Mouse click = shoot

const GAME_SIZE = 600; const PLAYER_SIZE = 18; const BULLET_SIZE = 5; const BOT_SIZE = 16;

function random(min, max) { return Math.random() * (max - min) + min; }

export default function FreeFireStyleMiniBattleRoyaleGame() { const canvasRef = useRef(null); const [running, setRunning] = useState(false); const [score, setScore] = useState(0); const keys = useRef({});

const gameState = useRef({ player: { x: GAME_SIZE / 2, y: GAME_SIZE / 2, hp: 100 }, bullets: [], bots: [], });

// Spawn bots function spawnBots(count = 6) { const bots = []; for (let i = 0; i < count; i++) { bots.push({ x: random(30, GAME_SIZE - 30), y: random(30, GAME_SIZE - 30), hp: 40, speed: random(0.5, 1.2), }); } gameState.current.bots = bots; }

// Controls useEffect(() => { const down = (e) => (keys.current[e.key.toLowerCase()] = true); const up = (e) => (keys.current[e.key.toLowerCase()] = false); window.addEventListener("keydown", down); window.addEventListener("keyup", up); return () => { window.removeEventListener("keydown", down); window.removeEventListener("keyup", up); }; }, []);

// Shooting useEffect(() => { const shoot = (e) => { if (!running) return; const rect = canvasRef.current.getBoundingClientRect(); const mx = e.clientX - rect.left; const my = e.clientY - rect.top;

const player = gameState.current.player;
  const angle = Math.atan2(my - player.y, mx - player.x);

  gameState.current.bullets.push({
    x: player.x,
    y: player.y,
    vx: Math.cos(angle) * 5,
    vy: Math.sin(angle) * 5,
  });
};

window.addEventListener("mousedown", shoot);
return () => window.removeEventListener("mousedown", shoot);

}, [running]);

// Game Loop useEffect(() => { if (!running) return;

const canvas = canvasRef.current;
const ctx = canvas.getContext("2d");

let animation;

function loop() {
  const state = gameState.current;
  const player = state.player;
