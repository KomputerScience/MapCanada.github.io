# MapCanada.github.io
A Fun social studies 7 learning escape room game
import { useState } from "react";
import { motion } from "framer-motion";

// ---- Canada Escape Room: Clue-Based, Educational, GitHub-Pages Friendly ----
// Each lock uses a clue. Players type an answer, can request a hint,
// and get a short "learn" explanation after solving.

// Helper to normalize answers (remove spaces, punctuation, accents, case)
const normalize = (s) =>
  s
    .toLowerCase()
    .normalize("NFD")
    .replace(/[\u0300-\u036f]/g, "") // strip accents
    .replace(/[^a-z0-9]/g, "");

const locks = [
  {
    id: 1,
    title: "Lock 1 – Coastal Riddle",
    clue:
      "You see a postcard: 🐻🌲⛴️ + a crown-shaped lighthouse. A note says: 'Capital of the province with rainy forests and big ferries.' Type the capital.",
    acceptable: ["victoria"],
    hint: "It's on Vancouver Island, not the big city on the mainland.",
    learn:
      "British Columbia's capital is Victoria, located on Vancouver Island. Vancouver is larger, but it's not the capital.",
  },
  {
    id: 2,
    title: "Lock 2 – Coordinate Lock",
    clue:
      "A brass compass is etched with '≈ 49°N, 97°W'. The plaque asks: 'Name the Canadian **provincial capital** closest to these coordinates.'",
    acceptable: ["winnipeg"],
    hint: "Think central Canada—prairies and the Red River.",
    learn:
      "Those coordinates point near Winnipeg, the capital of Manitoba, close to the U.S. border at the 49th parallel.",
  },
  {
    id: 3,
    title: "Lock 3 – Postal Wheel",
    clue:
      "A wheel shows the codes: QC → ?, ON → ?, AB → ?. Enter the **three capitals** in that order, separated by commas.",
    acceptable: [
      // Normalize will remove spaces/commas; accept common variants
      normalize("Quebec City, Toronto, Edmonton"),
      normalize("Quebec City,Toronto,Edmonton"),
    ],
    validate: (input) => normalize(input) === normalize("Quebec City, Toronto, Edmonton"),
    hint: "QC shares its name with the province; ON is Canada's most populous province; AB's capital is north of Calgary.",
    learn:
      "QC → Quebec City; ON → Toronto; AB → Edmonton. Postal codes match provinces: QC (Québec), ON (Ontario), AB (Alberta).",
  },
  {
    id: 4,
    title: "Lock 4 – River Route",
    clue:
      "A blue ribbon (the St. Lawrence River) flows past two locks that demand city names **in travel order** from west to east: 'Name the two major Quebec cities you pass after leaving Lake Ontario.'",
    acceptable: [normalize("Montreal, Quebec City")],
    validate: (input) => normalize(input) === normalize("Montreal, Quebec City"),
    hint: "First the island city famous for bagels and the Canadiens, then the walled capital.",
    learn:
      "Traveling down the St. Lawrence from Lake Ontario, you reach Montréal first, then Québec City.",
  },
  {
    id: 5,
    title: "Lock 5 – Northern Key",
    clue:
      "Carved in the ice wall: 🧊❄️🧭 and the word 'ᐃᖃᓗᐃᑦ' on a plaque. The keeper whispers: 'Name this **territorial capital**.'",
    acceptable: ["iqaluit"],
    hint: "It's the capital of Nunavut on Baffin Island.",
    learn:
      "Iqaluit is the capital of Nunavut. The inscription shows its Inuktitut name in syllabics.",
  },
  {
    id: 6,
    title: "Final Lock – Anagram Vault",
    clue:
      "A jumble of letters spins above the door: 'A TINY PEARL PROVINCE'. Unscramble the **province name** to open the vault.",
    acceptable: [normalize("Prince Edward Island")],
    validate: (input) => normalize(input) === normalize("Prince Edward Island"),
    hint: "It's Canada's smallest province by land area.",
    learn:
      "'A TINY PEARL PROVINCE' is an anagram of Prince Edward Island—home to Charlottetown.",
  },
];

export default function CanadaEscapeRoomClues() {
  const [step, setStep] = useState(0);
  const [input, setInput] = useState("");
  const [message, setMessage] = useState(
    "Welcome to the Canada Escape Room! Solve each lock using clues about provinces, territories, capitals, and major cities."
  );
  const [showHint, setShowHint] = useState(false);
  const [solved, setSolved] = useState(0);

  const current = locks[step];

  const isCorrect = (raw) => {
    const val = normalize(raw);
    if (current.validate) return current.validate(raw);
    return current.acceptable.some((a) => a === val);
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (!input.trim()) return;
    if (isCorrect(input)) {
      setSolved((s) => s + 1);
      setMessage(`✅ Correct! ${current.title} unlocked.`);
      setTimeout(() => {
        if (step < locks.length - 1) {
          setStep(step + 1);
          setInput("");
          setShowHint(false);
          setMessage("➡️ Next room opened. Read the new clue!");
        } else {
          setMessage("🎉 You escaped! All Canada clues solved. Great work!");
        }
      }, 900);
    } else {
      setMessage("❌ Not quite. Re-read the clue or try a hint.");
    }
  };

  const progress = Math.round(((solved) / locks.length) * 100);

  return (
    <div className="min-h-screen w-full flex flex-col items-center p-6 gap-6 bg-gradient-to-b from-slate-50 to-white">
      <motion.h1
        initial={{ y: -12, opacity: 0 }}
        animate={{ y: 0, opacity: 1 }}
        className="text-3xl md:text-4xl font-extrabold tracking-tight text-center"
      >
        Canada Escape Room: Clue Edition 🔐🗺️
      </motion.h1>

      {/* Progress Bar */}
      <div className="w-full max-w-2xl">
        <div className="h-3 bg-slate-200 rounded-full overflow-hidden">
          <div
            className="h-3 bg-blue-500 transition-all"
            style={{ width: `${progress}%` }}
            aria-label={`Progress ${progress}%`}
          />
        </div>
        <div className="mt-1 text-sm text-slate-600">{solved} / {locks.length} locks solved</div>
      </div>

      {/* Message */}
      <p className="text-base text-slate-700 text-center max-w-2xl">{message}</p>

      {/* Current Lock */}
      {step < locks.length && (
        <motion.div
          key={current.id}
          initial={{ opacity: 0, scale: 0.98 }}
          animate={{ opacity: 1, scale: 1 }}
          className="w-full max-w-2xl p-5 rounded-2xl shadow-sm border bg-white"
        >
          <div className="flex items-center justify-between mb-2">
            <h2 className="text-xl font-semibold">{current.title}</h2>
            <button
              onClick={() => setShowHint((v) => !v)}
              className="text-sm px-3 py-1 rounded-full border hover:bg-slate-50"
            >
              {showHint ? "Hide Hint" : "Show Hint"}
            </button>
          </div>

          <p className="text-slate-800 mb-3">{current.clue}</p>
          {showHint && (
            <div className="mb-3 text-sm text-slate-700 bg-slate-50 border rounded-xl p-3">
              💡 <span className="font-medium">Hint:</span> {current.hint}
            </div>
          )}

          <form onSubmit={handleSubmit} className="flex gap-2 mt-2">
            <input
              className="flex-1 px-4 py-2 rounded-xl border focus:outline-none focus:ring-2 focus:ring-blue-400"
              placeholder="Type your answer..."
              value={input}
              onChange={(e) => setInput(e.target.value)}
            />
            <button
              type="submit"
              className="px-5 py-2 rounded-xl bg-blue-600 text-white hover:bg-blue-700"
            >
              Unlock
            </button>
          </form>

          {/* Learning card appears when correct (after moving rooms) */}
          <div className="mt-4 text-sm text-slate-600">
            <span className="font-semibold">Learn:</span> {current.learn}
          </div>
        </motion.div>
      )}

      {/* Finished State */}
      {step >= locks.length && (
        <motion.div initial={{ opacity: 0 }} animate={{ opacity: 1 }} className="text-center">
          <p className="text-lg">You cracked every lock! 🔓</p>
          <p className="text-slate-700 mt-1">Replay to master the clues—or ask for more locks on time zones, physical regions, or Indigenous place names.</p>
        </motion.div>
      )}

      {/* Footer */}
      <div className="text-xs text-slate-500 mt-6">
        Built for Grade 7 social studies • Works on GitHub Pages (Vite/React or CRA)
      </div>
    </div>
  );
}
