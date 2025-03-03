# TFG_Psicologia
<!DOCTYPE html>
<html lang="ca">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Experiments Cognitius</title>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.4.0/p5.js"></script>
    <script src="https://unpkg.com/jspsych@7.3.3/jspsych.js"></script>
    <script src="https://unpkg.com/jspsych@7.3.3/plugins/jspsych-html-keyboard-response.js"></script>
    <script src="https://unpkg.com/jspsych@7.3.3/plugins/jspsych-survey-text.js"></script>
    <script src="https://unpkg.com/jspsych@7.3.3/plugins/jspsych-timeline-variables.js"></script>
    <link rel="stylesheet" href="https://unpkg.com/jspsych@7.3.3/css/jspsych.css">
    <style>
        body { font-family: Arial, sans-serif; text-align: center; margin: 20px; }
    </style>
</head>
<body>
    <h1>Experiments Cognitius</h1>
    <div id="start-container">
        <p><strong>Número identificatiu</strong></p>
        <p>Per a aquest experiment, es compararan els resultats recollits en dos moments diferents.</p>
        <p>Per tal d’identificar les seves respostes de manera anònima, li demanem que creï un número identificatiu format per les seves inicials i el seu any de naixement.</p>
        <p>Per exemple, si el seu nom és Joan Pérez Ruiz i va néixer el 1999, el seu número identificatiu seria: <strong>JPR1999</strong>.</p>
        <p>Aquest número s’utilitzarà exclusivament per relacionar les seves respostes en les diferents fases de l’estudi i garantir-ne l’anonimat.</p>
        <input type="text" id="participant-id" placeholder="Introdueix el teu número identificatiu" required>
        <button onclick="startExperiment()">Iniciar</button>
    </div>

    <script>
        function startExperiment() {
            let participantId = document.getElementById("participant-id").value;
            if (!participantId) {
                alert("Si us plau, introdueix el teu número identificatiu abans de començar.");
                return;
            }
            document.getElementById("start-container").style.display = "none";

            let jsPsych = initJsPsych({
                on_finish: function() {
                    jsPsych.data.get().localSave('csv', 'dades_experiment.csv');
                }
            });

            let instruccionsVisualSearch = {
                type: jsPsychHtmlKeyboardResponse,
                stimulus: "<p><strong>Instruccions: Cerca Visual</strong></p>" +
                          "<p>Has de trobar la lletra objectiu entre les distraccions.</p>" +
                          "<p>Prem la tecla corresponent quan la trobis.</p>" +
                          "<p>Prem qualsevol tecla per començar.</p>",
                choices: "ALL_KEYS"
            };

            let visualSearchTrial = {
                type: jsPsychHtmlKeyboardResponse,
                stimulus: function() {
                    return `<p>Troba la lletra objectiu: ${jsPsych.timelineVariable('target')}</p>`;
                },
                choices: ['t', 'q', 'y'],
                data: {
                    participant_id: participantId,
                    correct_response: jsPsych.timelineVariable('correct_response')
                },
                on_finish: function(data) {
                    data.correct = jsPsych.pluginAPI.compareKeys(data.response, data.correct_response);
                }
            };

            let visualSearchTimeline = {
                timeline: [visualSearchTrial],
                timeline_variables: [
                    { target: 'T', correct_response: 't' },
                    { target: 'Q', correct_response: 'q' },
                    { target: 'Y', correct_response: 'y' }
                ]
            };

            let instruccionsNBack = {
                type: jsPsychHtmlKeyboardResponse,
                stimulus: "<p><strong>Instruccions: 2-Back</strong></p>" +
                          "<p>Se't mostrarà una seqüència de lletres.</p>" +
                          "<p>Prem la tecla si la lletra actual és la mateixa que la de fa dos passos.</p>" +
                          "<p>Prem qualsevol tecla per començar.</p>",
                choices: "ALL_KEYS"
            };

            let nBackTrial = {
                type: jsPsychHtmlKeyboardResponse,
                stimulus: function() {
                    return `<p>${jsPsych.timelineVariable('stimulus')}</p>`;
                },
                choices: ['a', 'b', 'c', 'd'],
                data: {
                    participant_id: participantId,
                    correct_response: jsPsych.timelineVariable('correct_response')
                },
                on_finish: function(data) {
                    data.correct = jsPsych.pluginAPI.compareKeys(data.response, data.correct_response);
                }
            };

            let nBackTimeline = {
                timeline: [nBackTrial],
                timeline_variables: [
                    { stimulus: 'A', correct_response: 'a' },
                    { stimulus: 'B', correct_response: 'b' },
                    { stimulus: 'C', correct_response: 'c' },
                    { stimulus: 'D', correct_response: 'd' }
                ]
            };

            jsPsych.run([
                instruccionsVisualSearch,
                visualSearchTimeline,
                instruccionsNBack,
                nBackTimeline
            ]);
        }
    </script>
</body>
</html>
