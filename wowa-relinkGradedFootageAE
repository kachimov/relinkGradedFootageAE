// ============================================================================
// Relink Color Graded Footage for After Effects
// Version: 4.1
// 
// © 2026 Vladimir Vladimirovich Kachimov. All rights reserved.
// 
// LICENSE: NON-COMMERCIAL USE ONLY - NO MODIFICATIONS ALLOWED
// 
// This script is provided for personal and educational use only.
// 
// ✅ PERMITTED:
//    - Personal use in non-commercial projects
//    - Educational purposes
//    - Non-profit projects
// 
// ❌ PROHIBITED:
//    - Commercial use (projects that generate revenue)
//    - Use in for-profit companies
//    - Modification, derivative works, or redistribution
//    - Selling, sublicensing, or claiming as your own
// 
// For commercial licensing or permissions: kachimov.work@gmail.com
// 
// THIS SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND.
// ============================================================================


(function() {
    
    // Create window
    var win = new Window("palette", "Relink CC Files", undefined, {resizeable: true});
    win.orientation = "column";
    win.alignChildren = ["fill", "top"];
    win.spacing = 10;
    win.margins = 15;

    // Header
    var headerGrp = win.add("group");
    headerGrp.orientation = "row";
    headerGrp.alignChildren = ["left", "center"];
    headerGrp.add("statictext", undefined, "Суффикс (например, -cc):");
    
    var helpBtn = headerGrp.add("button", undefined, "?");
    helpBtn.preferredSize = [25, 20];

    // Help window
    helpBtn.onClick = function() {
        var helpWin = new Window("dialog", "Инструкция");
        helpWin.orientation = "column";
        helpWin.alignChildren = ["fill", "top"];
        helpWin.margins = 20;
        helpWin.spacing = 15;

        var helpText = helpWin.add("edittext", undefined, "", {multiline: true, readonly: true, scrollable: true});
        helpText.preferredSize = [450, 380];
        
        var txt = "";
        txt += "ЧТО ДЕЛАЕТ СКРИПТ:\n";
        txt += "Заменяет исходные файлы на покрашенные версии с суффиксом.\n\n";
        txt += "КАК ПОЛЬЗОВАТЬСЯ:\n";
        txt += "1. СУФФИКС: Введите текст для добавления к имени.\n";
        txt += "   Пример: video-test.mp4 -> video-test-cc.mp4\n\n";
        txt += "2. КОМПОЗИЦИЯ: Выберите композицию с роликом.\n\n";
        txt += "3. ПАПКА В PROJECT: Выберите папку с исходниками.\n\n";
        txt += "4. ПАПКА НА ДИСКЕ: Выберите папку с покрашенными файлами.\n\n";
        txt += "5. ЗАПУСК: Нажмите ПЕРЕЛИНКОВАТЬ.\n\n";
        txt += "БЕЗОПАСНОСТЬ:\n";
        txt += "- Меняются ТОЛЬКО файлы из выбранной папки\n";
        
        helpText.text = txt;

        var closeBtn = helpWin.add("button", undefined, "Закрыть");
        closeBtn.onClick = function() { helpWin.close(); };
        
        helpWin.center();
        helpWin.show();
    };
    
    // Suffix input
    var inputGrp = win.add("group");
    inputGrp.orientation = "row";
    inputGrp.alignChildren = ["fill", "center"];
    var suffixInput = inputGrp.add("edittext", undefined, "-cc");
    suffixInput.characters = 20;
    var clearBtn = inputGrp.add("button", undefined, "Очистить");
    clearBtn.preferredSize = [60, 20];

    // Composition selection
    var compGrp = win.add("group");
    compGrp.orientation = "column";
    compGrp.alignChildren = ["fill", "top"];
    compGrp.add("statictext", undefined, "Композиция:");
    
    var compRow = compGrp.add("group");
    compRow.orientation = "row";
    compRow.alignChildren = ["fill", "center"];
    var compNameText = compRow.add("statictext", undefined, "Не выбрана");
    compNameText.characters = 25;
    var selectCompBtn = compRow.add("button", undefined, "Выбрать");
    
    // Project folder selection
    var projFolderGrp = win.add("group");
    projFolderGrp.orientation = "column";
    projFolderGrp.alignChildren = ["fill", "top"];
    projFolderGrp.add("statictext", undefined, "Папка в окне Project (источник):");
    
    var projFolderRow = projFolderGrp.add("group");
    projFolderRow.orientation = "row";
    projFolderRow.alignChildren = ["fill", "center"];
    var projFolderPathText = projFolderRow.add("statictext", undefined, "Не выбрана");
    projFolderPathText.characters = 25;
    var selectProjFolderBtn = projFolderRow.add("button", undefined, "Выбрать");
    
    // Disk folder selection
    var diskFolderGrp = win.add("group");
    diskFolderGrp.orientation = "column";
    diskFolderGrp.alignChildren = ["fill", "top"];
    diskFolderGrp.add("statictext", undefined, "Папка на диске (покрашенные):");
    
    var diskFolderRow = diskFolderGrp.add("group");
    diskFolderRow.orientation = "row";
    diskFolderRow.alignChildren = ["fill", "center"];
    var diskFolderPathText = diskFolderRow.add("statictext", undefined, "Не выбрана");
    diskFolderPathText.characters = 25;
    var selectDiskFolderBtn = diskFolderRow.add("button", undefined, "Выбрать");
    
    // Run button
    var btnGrp = win.add("group");
    btnGrp.orientation = "row";
    btnGrp.alignChildren = ["fill", "center"];
    var runBtn = btnGrp.add("button", undefined, "ПЕРЕЛИНКОВАТЬ");
    runBtn.preferredSize = [undefined, 30];
    
    // Progress bar
    var progressBar = win.add("progressbar", undefined, 0, 100);
    progressBar.preferredSize = [undefined, 10];
    
    // Log box
    var logBox = win.add("edittext", undefined, "", {multiline: true, readonly: true, scrollable: true});
    logBox.preferredSize = [undefined, 150];

    var selectedComp = null;
    var selectedProjFolder = null;
    var selectedDiskFolder = null;

    // Clear button
    clearBtn.onClick = function() { 
        suffixInput.text = ""; 
        suffixInput.active = true; 
    };

    // Select composition button
    selectCompBtn.onClick = function() {
        var comps = [];
        var i;
        for (i = 1; i <= app.project.items.length; i++) {
            if (app.project.item(i) instanceof CompItem) {
                comps.push(app.project.item(i));
            }
        }
        
        if (comps.length === 0) {
            alert("В проекте нет композиций!", "Ошибка");
            return;
        }
        
        var compWin = new Window("dialog", "Выберите композицию");
        compWin.orientation = "column";
        compWin.alignChildren = ["fill", "top"];
        compWin.margins = 20;
        
        var compList = compWin.add("listbox", undefined, undefined, {multiselect: false});
        compList.preferredSize = [300, 200];
        
        var j;
        for (j = 0; j < comps.length; j++) {
            compList.add("item", comps[j].name);
            if (app.project.activeItem === comps[j]) {
                compList.selection = j;
            }
        }
        
        var okBtn = compWin.add("button", undefined, "OK");
        var cancelBtn = compWin.add("button", undefined, "Отмена");
        
        okBtn.onClick = function() {
            if (compList.selection !== null) {
                selectedComp = comps[compList.selection.index];
                compNameText.text = selectedComp.name;
                log("Выбрана композиция: " + selectedComp.name);
            }
            compWin.close();
        };
        
        cancelBtn.onClick = function() { compWin.close(); };
        
        compWin.center();
        compWin.show();
    };

    // Select project folder button
    selectProjFolderBtn.onClick = function() {
        var folders = [];
        var i;
        for (i = 1; i <= app.project.items.length; i++) {
            if (app.project.item(i) instanceof FolderItem) {
                folders.push(app.project.item(i));
            }
        }
        
        if (folders.length === 0) {
            alert("В проекте нет папок!", "Ошибка");
            return;
        }
        
        var folderWin = new Window("dialog", "Выберите папку в окне Project");
        folderWin.orientation = "column";
        folderWin.alignChildren = ["fill", "top"];
        folderWin.margins = 20;
        
        var folderList = folderWin.add("listbox", undefined, undefined, {multiselect: false});
        folderList.preferredSize = [300, 200];
        
        var j;
        for (j = 0; j < folders.length; j++) {
            folderList.add("item", folders[j].name);
        }
        
        var okBtn = folderWin.add("button", undefined, "OK");
        var cancelBtn = folderWin.add("button", undefined, "Отмена");
        
        okBtn.onClick = function() {
            if (folderList.selection !== null) {
                selectedProjFolder = folders[folderList.selection.index];
                projFolderPathText.text = selectedProjFolder.name;
                log("Выбрана папка Project: " + selectedProjFolder.name);
            }
            folderWin.close();
        };
        
        cancelBtn.onClick = function() { folderWin.close(); };
        
        folderWin.center();
        folderWin.show();
    };

    // Select disk folder button
    selectDiskFolderBtn.onClick = function() {
        var f = Folder.selectDialog("Выберите папку с покрашенными файлами");
        if (f) {
            selectedDiskFolder = f;
            diskFolderPathText.text = f.displayName;
            log("Выбрана папка на диске: " + f.fsName);
        }
    };

    // Run button
    runBtn.onClick = function() { 
        executeRelink(); 
    };

    // Log function
    function log(msg) {
        if (logBox.text === "") { 
            logBox.text = msg; 
        } else { 
            logBox.text += "\n" + msg; 
        }
    }
    
    // Clear log
    function clearLog() { 
        logBox.text = ""; 
    }

    // Helper: check if string ends with suffix
    function stringEndsWith(str, suffix) {
        if (suffix.length > str.length) return false;
        return str.indexOf(suffix, str.length - suffix.length) !== -1;
    }

    // Helper: check if footage is used in composition
    function isFootageUsedInComp(footageItem, compItem) {
        var i;
        for (i = 1; i <= compItem.layers.length; i++) {
            var layer = compItem.layer(i);
            if (layer.source && layer.source === footageItem) {
                return true;
            }
        }
        return false;
    }

    // Helper: replace footage in all layers of composition
    function replaceFootageInComp(oldFootage, newFootage, compItem) {
        var replaced = false;
        var i;
        for (i = 1; i <= compItem.layers.length; i++) {
            var layer = compItem.layer(i);
            if (layer.source === oldFootage) {
                layer.replaceSource(newFootage, false);
                replaced = true;
            }
        }
        return replaced;
    }

    // Main logic
    function executeRelink() {
        clearLog();
        
        var suffix = suffixInput.text;
        
        // Validation
        if (suffix === "") { 
            alert("Ошибка: Введите суффикс.", "Ошибка"); 
            return; 
        }
        
        if (suffix.indexOf("\\") !== -1 || suffix.indexOf("/") !== -1 || 
            suffix.indexOf(":") !== -1 || suffix.indexOf("*") !== -1 || 
            suffix.indexOf("?") !== -1 || suffix.indexOf('"') !== -1 || 
            suffix.indexOf("<") !== -1 || suffix.indexOf(">") !== -1 || 
            suffix.indexOf("|") !== -1) { 
            alert("Ошибка: Суффикс содержит запрещенные символы.", "Ошибка"); 
            return; 
        }
        
        if (!selectedComp) { 
            alert("Ошибка: Выберите композицию.", "Ошибка"); 
            return; 
        }
        
        if (!selectedProjFolder) { 
            alert("Ошибка: Выберите папку в окне Project.", "Ошибка"); 
            return; 
        }
        
        if (!selectedDiskFolder || !selectedDiskFolder.exists) { 
            alert("Ошибка: Выберите папку на диске.", "Ошибка"); 
            return; 
        }

        app.beginUndoGroup("Relink Color Graded Footage");
        
        // Get all footage items from selected project folder
        var footageItems = [];
        var i;
        for (i = 1; i <= app.project.items.length; i++) {
            var item = app.project.item(i);
            if (item instanceof FootageItem && item.parentFolder === selectedProjFolder) {
                footageItems.push(item);
            }
        }
        
        if (footageItems.length === 0) {
            alert("В выбранной папке нет файлов!", "Ошибка");
            app.endUndoGroup();
            return;
        }
        
        // Check which files are used in composition
        var usedInComp = [];
        var notUsed = [];
        for (i = 0; i < footageItems.length; i++) {
            if (isFootageUsedInComp(footageItems[i], selectedComp)) {
                usedInComp.push(footageItems[i]);
            } else {
                notUsed.push(footageItems[i]);
            }
        }
        
        if (usedInComp.length === 0) {
            var confirmMsg = "ВНИМАНИЕ!\n\n";
            confirmMsg += "Ни один файл из папки '" + selectedProjFolder.name + "' не используется в композиции '" + selectedComp.name + "'.\n\n";
            confirmMsg += "Продолжить перелинковку всех файлов в папке?";
            
            if (!confirm(confirmMsg, "Предупреждение")) {
                app.endUndoGroup();
                return;
            }
            usedInComp = footageItems;
        }
        
        var totalItems = usedInComp.length;
        var relinkedCount = 0;
        var skippedCount = 0;
        var missingCount = 0;
        var alreadyHasSuffixCount = 0;
        var missingFilesList = [];

        progressBar.value = 0;
        log("Начинаю перелинковку... Суффикс: " + suffix);
        log("Композиция: " + selectedComp.name);
        log("Папка Project: " + selectedProjFolder.name);
        log("Папка на диске: " + selectedDiskFolder.fsName);
        log("Файлов в папке: " + footageItems.length);
        log("Используется в композиции: " + usedInComp.length);
        if (notUsed.length > 0) {
            log("Не используется: " + notUsed.length);
        }
        log("----------------------------------------");

        var k;
        for (k = 0; k < totalItems; k++) {
            var item = usedInComp[k];
            progressBar.value = Math.round(((k + 1) / totalItems) * 100);

            var originalName = item.name;
            var lastDotIndex = originalName.lastIndexOf(".");
            var baseName = "";
            var extension = "";
            
            if (lastDotIndex !== -1) {
                baseName = originalName.substring(0, lastDotIndex);
                extension = originalName.substring(lastDotIndex); 
            } else {
                baseName = originalName;
            }

            // Check if already has suffix
            if (stringEndsWith(baseName, suffix)) {
                alreadyHasSuffixCount++;
                log("ПРОПУЩЕНО (уже с суффиксом): " + originalName);
                continue; 
            }

            var newFileName = baseName + suffix + extension;
            var newFilePath = selectedDiskFolder.fsName + "/" + newFileName;
            var newFile = new File(newFilePath);

            // Check alternative case for extension (Mac compatibility)
            if (!newFile.exists && extension !== "") {
                var altExtension = "";
                if (extension === extension.toLowerCase()) {
                    altExtension = extension.toUpperCase();
                } else {
                    altExtension = extension.toLowerCase();
                }
                var altFileName = baseName + suffix + altExtension;
                var altFilePath = selectedDiskFolder.fsName + "/" + altFileName;
                var altFile = new File(altFilePath);
                if (altFile.exists) {
                    newFile = altFile;
                }
            }

            if (newFile.exists) {
                try {
                    // Import new file
                    var importOptions = new ImportOptions(newFile);
                    var newFootage = app.project.importFile(importOptions);
                    
                    // Move new footage to same folder as old one
                    newFootage.parentFolder = item.parentFolder;
                    
                    // Replace in composition
                    var replaced = replaceFootageInComp(item, newFootage, selectedComp);
                    
                    if (replaced) {
                        // Remove old footage
                        item.remove();
                        relinkedCount++;
                        log("ЗАМЕНЕНО: " + originalName + " -> " + newFileName);
                    } else {
                        // If not used in comp, remove imported file
                        newFootage.remove();
                        log("ПРОПУЩЕНО (не используется в слоях): " + originalName);
                        skippedCount++;
                    }
                } catch (e) {
                    log("ОШИБКА: " + originalName + " -> " + e.message);
                    missingCount++;
                }
            } else {
                missingCount++;
                if (missingFilesList.length < 10) {
                    missingFilesList.push(newFileName);
                }
                log("НЕ НАЙДЕНО: " + newFileName);
            }
        }

        app.endUndoGroup();
        progressBar.value = 100;

        var reportMsg = "ГОТОВО!\n";
        reportMsg += "Успешно: " + relinkedCount + "\n";
        reportMsg += "Пропущено: " + skippedCount + "\n";
        reportMsg += "Уже с суффиксом: " + alreadyHasSuffixCount + "\n";
        reportMsg += "НЕ НАЙДЕНО: " + missingCount;
        
        log("----------------------------------------");
        log(reportMsg);
        
        if (missingFilesList.length > 0) {
            log("\nСписок отсутствующих файлов:");
            var m;
            for (m = 0; m < missingFilesList.length; m++) {
                log(" - " + missingFilesList[m]);
            }
        }
        
        var alertMsg = "Перелинковка завершена!\n\n";
        alertMsg += "Успешно: " + relinkedCount + "\n";
        alertMsg += "Не найдено: " + missingCount;
        alert(alertMsg, "Результат");
    }

    // Show window
    if (win instanceof Window) { 
        win.center(); 
        win.show(); 
    }

})();
