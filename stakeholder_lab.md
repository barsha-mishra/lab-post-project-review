# Stakeholder Impact Lab – Podcast Studio

## Part 1: Project snapshot

**Project:** Podcast Studio (group mini-project, 3 people)

Podcast Studio was meant to solve the problem that reading long texts takes time and focus, and not everyone can sit down and read. The main intended user is a **student** who wants to turn long study notes into audio to listen to on the go; secondary users include teachers and professionals with the same need. We built an MVP in Python: the user uploads or pastes notes, an LLM turns them into a podcast-style script, and a text-to-speech API turns the script into audio, all through a simple Gradio interface. My part was the data input step (`load_notes`), which reads and cleans the notes before they go to the LLM.

## Part 2: Stakeholder impact

| Stakeholder | Type | Role / relationship | What they need | Risk if ignored | Influence | Interest |
|---|---|---|---|---|---|---|
| Students (main users) | Direct | Upload notes and listen to the audio | Accurate, clear audio of their own notes | LLM changes or invents content, so they study wrong facts | Low | High |
| Group teammates (Parts B & C) | Direct | Build the script and audio steps that use my `load_notes` output | Clean text in the agreed format | The pipeline breaks and the demo fails | High | High |
| Bootcamp instructor | Indirect | Sponsor / evaluator of the project | A working demo that meets the requirements | Project is not accepted or graded poorly | High | High |
| OpenAI (API provider) | Indirect | Provides the LLM and text-to-speech APIs | Use within their terms and rate limits | Key is blocked or limited and the app stops working | High | Low |
| Future maintainer (future me or teammates) | Indirect | Fixes or extends the code later | Readable code, comments, and a README | Nobody understands how to fix or update the app | Low | Low |
| Whoever pays the API bill | Indirect | Pays for every script and audio file generated | Predictable, controlled costs | Long notes create unexpectedly high costs | High | Low |
| **Stretch:** Students with visual impairment or dyslexia | Direct | Rely on audio instead of reading | An accessible interface and clear, well-paced audio | The tool excludes the people who need audio the most | Low | High |

**Why the stretch row matters:** Students with visual impairment or dyslexia are easy to forget because the demo assumes a typical user. But they depend on audio the most, so if the Gradio interface isn't accessible (for example, it doesn't work with a screen reader), the project fails its most important users.

## Part 3: From demo to "real project"

### 1. Operations
In our demo, nobody monitors the app; if the OpenAI API fails or is slow, the user just sees an error in Gradio and we only notice if we are testing it ourselves. For production, we would add logging for each step (load notes → script → audio), alerts when errors increase, and a clear owner who fixes problems when the app goes down.

### 2. Security & compliance
In the demo, the API key is stored locally on our own machines and there is no login, so anyone with the link can use the app and spend our API credits. For production, we would store the key in a secrets manager, add user login, and set usage limits per user so one person cannot run up the bill.

### 3. Data lifecycle
Students might upload notes with personal information (names, grades, private comments), and our demo has no rule for how long notes, scripts, or audio files are kept. For production, we would delete uploaded notes and generated files after a set time, tell users clearly what happens to their data, and check what OpenAI does with data sent to its API.

### 4. Scope beyond the demo
My `load_notes` function mostly handles the happy path: a normal text file with readable content. For production, it would need to handle empty files, very long notes (split into chunks so the LLM and text-to-speech don't fail), other file types like PDF, and other languages. If the text-to-speech API fails, the app should still show the script as text so the user gets something useful.


## Part 4: Revision brief

**Before:**
At the start, our goal was to build a working pipeline that turns any notes into a podcast: upload text, choose one of 8 languages, generate a script with an LLM, and create downloadable audio with text-to-speech, shown in a Gradio interface. Success meant that the demo worked end-to-end with our own test notes during the presentation.

**After:**
Now we would narrow the MVP to one clear user, students turning study notes into audio, and only support `.txt` and `.md` files under a set length. We would launch with fewer languages, only the ones we have properly tested, and add the rest of the 8 once their script and audio quality is checked. Success would mean more than "the demo runs": the audio must stay faithful to the original notes, the app must handle empty or very long files without crashing, and it must show the script if audio fails. We would also add a security and cost check before release (API key in a secrets manager, usage limits per user), an accessibility check for students who rely on audio, and a clear rule for how long generated audio files are stored before download links expire.