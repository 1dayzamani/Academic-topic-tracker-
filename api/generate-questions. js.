export default async function handler(req, res) {
  if (req.method !== "POST") {
    return res.status(405).json({ error: "Method not allowed" });
  }

  try {
    const { topics, qType } = req.body;

    const prompt = `You are writing a K-12 exam. Write exactly 6 exam questions covering ONLY these topics (do not go outside them): ${topics.join(
      "; "
    )}.
Question style: ${qType === "Mixed" ? "a mix of multiple choice and short answer" : qType}.
Return ONLY a JSON array of objects, each with keys "topic", "question", and "answer" (answer should be brief). No preamble, no markdown.`;

    const response = await fetch("https://api.anthropic.com/v1/messages", {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
        "x-api-key": process.env.ANTHROPIC_API_KEY,
        "anthropic-version": "2023-06-01",
      },
      body: JSON.stringify({
        model: "claude-sonnet-4-6",
        max_tokens: 1200,
        messages: [{ role: "user", content: [{ type: "text", text: prompt }] }],
      }),
    });

    const data = await response.json();
    const text = data.content?.map((b) => b.text || "").join("\n") || "";
    const clean = text.replace(/```json|```/g, "").trim();
    const questions = JSON.parse(clean);

    res.status(200).json({ questions });
  } catch (err) {
    console.error(err);
    res.status(500).json({ error: "Failed to generate questions" });
  }
}
