import { useState, useEffect } from "react";

type Player = "X" | "O";
type Cell = Player | null;
type Board = Cell[];
type Mode = "friend" | "computer" | null;

const WIN_LINES = [
  [0, 1, 2], [3, 4, 5], [6, 7, 8],
  [0, 3, 6], [1, 4, 7], [2, 5, 8],
  [0, 4, 8], [2, 4, 6],
];

function getWinner(board: Board): { winner: Player; line: number[] } | null {
  for (const [a, b, c] of WIN_LINES) {
    if (board[a] && board[a] === board[b] && board[a] === board[c]) {
      return { winner: board[a] as Player, line: [a, b, c] };
    }
  }
  return null;
}

function bestMove(board: Board): number {
  function score(b: Board, depth: number, isMax: boolean): number {
    const w = getWinner(b);
    if (w) return w.winner === "O" ? 10 - depth : depth - 10;
    if (b.every(Boolean)) return 0;
    let best = isMax ? -Infinity : Infinity;
    for (let i = 0; i < 9; i++) {
      if (!b[i]) {
        b[i] = isMax ? "O" : "X";
        const s = score(b, depth + 1, !isMax);
        b[i] = null;
        best = isMax ? Math.max(best, s) : Math.min(best, s);
      }
    }
    return best;
  }
  let best = -Infinity, move = -1;
  for (let i = 0; i < 9; i++) {
    if (!board[i]) {
      board[i] = "O";
      const s = score(board, 0, false);
      board[i] = null;
      if (s > best) { best = s; move = i; }
    }
  }
  return move;
}

function whoseTurn(board: Board): Player {
  const xs = board.filter(c => c === "X").length;
  const os = board.filter(c => c === "O").length;
  return xs <= os ? "X" : "O";
}

function ModeSelect({ onSelect }: { onSelect: (m: "friend" | "computer") => void }) {
  return (
    <div className="relative min-h-screen flex flex-col items-center justify-center overflow-hidden px-4 py-10">
      <div className="scanline fixed inset-0 z-0" />
      <div className="fixed inset-0 z-0 opacity-10" style={{
        backgroundImage: "linear-gradient(hsl(195 100% 50%/.3) 1px,transparent 1px),linear-gradient(90deg,hsl(195 100% 50%/.3) 1px,transparent 1px)",
        backgroundSize: "60px 60px",
      }} />
      <div className="relative z-10 flex flex-col items-center gap-10 w-full max-w-sm text-center">
        <div>
          <h1 className="text-4xl sm:text-5xl font-black tracking-[0.3em] uppercase neon-text mb-1" style={{ fontFamily: "'Orbitron',monospace" }}>
            TIC TAC TOE
          </h1>
          <p className="text-xs tracking-[0.4em] uppercase" style={{ color: "hsl(210 60% 45%)" }}>CYBER EDITION</p>
        </div>
        <div className="flex flex-col gap-4 w-full">
          <p className="text-xs tracking-[0.3em] uppercase mb-2" style={{ color: "hsl(210 50% 50%)", fontFamily: "'Orbitron',monospace" }}>
            SELECT MODE
          </p>
          {([
            { id: "friend", label: "PLAY WITH FRIEND", sub: "Two players — same device", icon: "👤 + 👤", hue: 195 },
            { id: "computer", label: "PLAY VS COMPUTER", sub: "You are X — AI plays O", icon: "👤 + 🤖", hue: 280 },
          ] as const).map(({ id, label, sub, icon, hue }) => (
            <button
              key={id}
              onClick={() => onSelect(id)}
              className="w-full py-5 px-6 rounded border font-black tracking-[0.2em] uppercase text-base transition-all duration-200 hover:scale-[1.03] active:scale-95"
              style={{
                fontFamily: "'Orbitron',monospace",
                background: "hsl(220 25% 7%)",
                borderColor: `hsl(${hue} 100% 55% / 0.5)`,
                color: `hsl(${hue} 100% 70%)`,
                boxShadow: `0 0 14px hsl(${hue} 100% 50%/.2),inset 0 0 12px hsl(${hue} 100% 50%/.05)`,
              }}
            >
              <span className="flex flex-col items-center gap-1">
                <span className="text-2xl">{icon}</span>
                <span>{label}</span>
                <span className="text-xs font-normal tracking-widest normal-case" style={{ color: `hsl(${hue} 80% 60%)`, opacity: 0.75 }}>{sub}</span>
              </span>
            </button>
          ))}
        </div>
      </div>
    </div>
  );
}

const EMPTY: Board = Array(9).fill(null);

export default function TicTacToe() {
  const [mode, setMode] = useState<Mode>(null);
  const [board, setBoard] = useState<Board>([...EMPTY]);
  const [winLine, setWinLine] = useState<number[] | null>(null);
  const [gameOver, setGameOver] = useState(false);
  const [isDraw, setIsDraw] = useState(false);
  const [scores, setScores] = useState({ X: 0, O: 0 });

  const winner = getWinner(board)?.winner ?? null;
  const current: Player = gameOver ? (winner ?? "X") : whoseTurn(board);
  const isVsComputer = mode === "computer";
  const aiTurn = isVsComputer && current === "O" && !gameOver;

  useEffect(() => {
    if (!aiTurn) return;
    const boardCopy = [...board] as Board;
    const t = setTimeout(() => {
      const move = bestMove(boardCopy);
      if (move === -1) return;
      const next = [...boardCopy] as Board;
      next[move] = "O";
      applyBoard(next);
    }, 120);
    return () => clearTimeout(t);
    // eslint-disable-next-line react-hooks/exhaustive-deps
  }, [aiTurn, board.join(",")]);

  function applyBoard(next: Board) {
    setBoard(next);
    const win = getWinner(next);
    if (win) {
      setWinLine(win.line);
      setScores(prev => ({ ...prev, [win.winner]: prev[win.winner] + 1 }));
      setGameOver(true);
      return;
    }
    if (next.every(Boolean)) {
      setIsDraw(true);
      setGameOver(true);
    }
  }

  function handleClick(i: number) {
    if (board[i] || gameOver || aiTurn) return;
    const next = [...board] as Board;
    next[i] = current;
    applyBoard(next);
  }

  function resetGame() {
    setBoard([...EMPTY]);
    setWinLine(null);
    setGameOver(false);
    setIsDraw(false);
  }

  function goToMenu() {
    setMode(null);
    setBoard([...EMPTY]);
    setWinLine(null);
    setGameOver(false);
    setIsDraw(false);
    setScores({ X: 0, O: 0 });
  }

  const statusText = (() => {
    if (winner) return isVsComputer ? (winner === "X" ? "YOU WIN! 🎉" : "AI WINS!") : `PLAYER ${winner} WINS`;
    if (isDraw) return "DRAW — SYSTEM TIE";
    if (aiTurn) return "AI THINKING...";
    return isVsComputer ? "YOUR TURN" : `PLAYER ${current}'S TURN`;
  })();

  const statusHue = (winner === "X" || (!winner && !isDraw && current === "X" && !aiTurn)) ? 195 : 280;

  if (!mode) return <ModeSelect onSelect={setMode} />;

  return (
    <div className="relative min-h-screen flex flex-col items-center justify-center overflow-hidden px-4 py-10">
      <div className="scanline fixed inset-0 z-0" />
      <div className="fixed inset-0 z-0 opacity-10" style={{
        backgroundImage: "linear-gradient(hsl(195 100% 50%/.3) 1px,transparent 1px),linear-gradient(90deg,hsl(195 100% 50%/.3) 1px,transparent 1px)",
        backgroundSize: "60px 60px",
      }} />
      <div className="relative z-10 flex flex-col items-center gap-6 w-full max-w-md">
        <div className="text-center">
          <h1 className="text-4xl sm:text-5xl font-black tracking-[0.3em] uppercase neon-text mb-1" style={{ fontFamily: "'Orbitron',monospace" }}>
            TIC TAC TOE
          </h1>
          <p className="text-xs tracking-[0.4em] uppercase" style={{ color: "hsl(210 60% 45%)" }}>
            {isVsComputer ? "VS COMPUTER" : "2-PLAYER MODE"}
          </p>
        </div>
        <div className="flex gap-6 w-full">
          {(["X", "O"] as Player[]).map(p => (
            <div key={p} className="flex-1 flex flex-col items-center gap-1 py-3 px-4 rounded border" style={{
              background: "hsl(220 25% 7%)",
              borderColor: p === "X" ? "hsl(195 100% 45%/.5)" : "hsl(280 100% 55%/.5)",
              boxShadow: p === "X" ? "0 0 10px hsl(195 100% 50%/.15)" : "0 0 10px hsl(280 100% 50%/.15)",
            }}>
              <span className={`text-xl font-black tracking-widest ${p === "X" ? "neon-x" : "neon-o"}`} style={{ fontFamily: "'Orbitron',monospace" }}>{p}</span>
              <span className="text-3xl font-black tabular-nums" style={{ color: p === "X" ? "hsl(195 100% 70%)" : "hsl(280 100% 75%)" }}>
                {scores[p]}
              </span>
              <span className="text-xs tracking-[0.25em] uppercase" style={{ color: "hsl(210 40% 45%)" }}>
                {isVsComputer ? (p === "X" ? "YOU" : "AI") : `PLAYER ${p}`}
              </span>
            </div>
          ))}
        </div>
        <div className="w-full py-2 px-6 rounded border text-center" style={{
          background: "hsl(220 25% 6%)",
          borderColor: `hsl(${statusHue} 100% 45%/.3)`,
        }}>
          <p className="text-sm font-bold tracking-[0.3em] uppercase"
            style={{ fontFamily: "'Orbitron',monospace", color: `hsl(${statusHue} 100% 65%)`, textShadow: `0 0 10px hsl(${statusHue} 100% 50%/.5)` }}>
            {statusText}
          </p>
        </div>
        <div className="grid gap-3 p-4 rounded-lg border neon-border w-full" style={{
          gridTemplateColumns: "repeat(3,1fr)",
          background: "hsl(220 25% 5%)",
          aspectRatio: "1",
        }}>
          {board.map((cell, i) => {
            const isWin = winLine?.includes(i) ?? false;
            const clickable = !cell && !gameOver && !aiTurn;
            const hue = cell === "X" ? 195 : 280;
            return (
              <button
                key={i}
                onClick={() => handleClick(i)}
                disabled={!clickable}
                className={`flex items-center justify-center rounded border text-5xl sm:text-6xl font-black transition-all duration-150 disabled:cursor-not-allowed ${clickable ? "cell-hover" : ""}`}
                style={{
                  aspectRatio: "1",
                  fontFamily: "'Orbitron',monospace",
                  background: isWin ? `hsl(${hue} 100% 50%/.07)` : "hsl(220 20% 8%)",
                  borderColor: isWin ? `hsl(${hue} 100% 60%)` : "hsl(210 60% 20%/.6)",
                  boxShadow: isWin ? `0 0 18px hsl(${hue} 100% 50%/.5),inset 0 0 12px hsl(${hue} 100% 50%/.12)` : "none",
                }}
              >
                {cell && (
                  <span className={cell === "X" ? "neon-x" : "neon-o"} style={{ animation: "pop .15s ease-out", display: "block" }}>
                    {cell}
                  </span>
                )}
              </button>
            );
          })}
        </div>
        <div className="flex gap-4 w-full">
          {[
            { label: "NEW GAME", hue: 195, fn: resetGame },
            { label: "MENU", hue: 280, fn: goToMenu },
          ].map(({ label, hue, fn }) => (
            <button key={label} onClick={fn}
              className="flex-1 py-3 px-4 rounded border font-bold tracking-[0.2em] uppercase text-sm transition-all duration-200 active:scale-95"
              style={{
                fontFamily: "'Orbitron',monospace",
                background: "hsl(220 25% 7%)",
                borderColor: `hsl(${hue} 100% 50%/.45)`,
                color: `hsl(${hue} 100% 68%)`,
                boxShadow: `0 0 8px hsl(${hue} 100% 50%/.15)`,
              }}>
              {label}
            </button>
          ))}
        </div>
      </div>
      <style>{`
        @keyframes pop { from { opacity:0; transform:scale(.5); } to { opacity:1; transform:scale(1); } }
      `}</style>
    </div>
  );
}
