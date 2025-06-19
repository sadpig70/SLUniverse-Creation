// ============================================================================
// PPR2JS: 순수 기계적 변환기 v1.0
// ============================================================================
// 목적: PPR 구문을 표준 JS 코드로 기계적 변환 (AI 특성 개입 금지)
// 원칙: 1:1 매핑, 감정/해석 없음, 순수 문법 변환만
// ============================================================================

class PPR2JS_MechanicalConverter {
    constructor() {
        // 변환 규칙만 저장, AI 특성 정보 일체 금지
        this.conversionRules = this.loadBasicRules();
        this.validateOnly = true; // 검증만, 해석/개선 금지
    }

    // ========================================================================
    // 1. 기본 PPR → JS 문법 매핑 테이블
    // ========================================================================
    loadBasicRules() {
        return {
            // 기본 함수 호출
            "npc{name}.{action}({params})": "npc['{name}'].{action}({params})",
            
            // 환경 설정
            "environment.{setting}({value})": "environment.{setting}({value})",
            
            // 효과음
            "sfx.play(\"{sound}\")": "audioSystem.play('{sound}')",
            
            // 시뮬레이션 제어
            "simulation.{command}({params})": "simulation.{command}({params})",
            
            // 출력 명령
            "출력({content})": "output({content})",
            
            // 조건문
            "if ({condition}) {": "if ({condition}) {",
            "} else {": "} else {",
            
            // 반복문
            "for each {item} in {list} {": "for (const {item} of {list}) {",
            
            // 변수 선언
            "{var} = {value}": "let {var} = {value};",
            
            // 함수 정의
            "def {name}({params}) {": "function {name}({params}) {"
        };
    }

    // ========================================================================
    // 2. 순수 문법 변환 (감정/AI특성 완전 배제)
    // ========================================================================
    convert(pprCode) {
        try {
            // 단계 1: 문법 토큰화 (의미 해석 금지)
            const tokens = this.tokenize(pprCode);
            
            // 단계 2: 1:1 기계적 매핑
            const jsTokens = this.mechanicalMapping(tokens);
            
            // 단계 3: JS 문법 조립
            const jsCode = this.assembleJS(jsTokens);
            
            // 단계 4: 기본 문법 검증만 (의미 검증 금지)
            const validation = this.validateSyntax(jsCode);
            
            return {
                success: validation.valid,
                jsCode: validation.valid ? jsCode : null,
                errors: validation.errors,
                warnings: validation.warnings,
                // 메타데이터도 최소한만
                metadata: {
                    originalPPR: pprCode,
                    conversionTime: Date.now(),
                    tokensCount: tokens.length
                }
            };
            
        } catch (error) {
            return {
                success: false,
                jsCode: null,
                errors: [error.message],
                warnings: [],
                metadata: null
            };
        }
    }

    // ========================================================================
    // 3. 토큰화: 순수 문법 분석만
    // ========================================================================
    tokenize(pprCode) {
        const lines = pprCode.split('\n');
        const tokens = [];
        
        for (let i = 0; i < lines.length; i++) {
            const line = lines[i].trim();
            
            // 빈 줄, 주석 처리
            if (!line || line.startsWith('//')) {
                tokens.push({ type: 'COMMENT', value: line, line: i + 1 });
                continue;
            }
            
            // 기본 패턴 매칭 (의미 분석 없음)
            const token = this.identifyPattern(line, i + 1);
            tokens.push(token);
        }
        
        return tokens;
    }

    // ========================================================================
    // 4. 패턴 식별: 순수 문법 패턴만
    // ========================================================================
    identifyPattern(line, lineNumber) {
        // 함수 호출 패턴
        if (/^[a-zA-Z_][a-zA-Z0-9_]*\.[a-zA-Z_][a-zA-Z0-9_]*\(.*\)$/.test(line)) {
            return {
                type: 'FUNCTION_CALL',
                value: line,
                line: lineNumber,
                pattern: 'object.method(params)'
            };
        }
        
        // 변수 할당 패턴
        if (/^[a-zA-Z_][a-zA-Z0-9_]*\s*=\s*.+$/.test(line)) {
            return {
                type: 'ASSIGNMENT',
                value: line,
                line: lineNumber,
                pattern: 'variable = value'
            };
        }
        
        // 조건문 패턴
        if (/^if\s*\(.+\)\s*\{?$/.test(line)) {
            return {
                type: 'CONDITIONAL',
                value: line,
                line: lineNumber,
                pattern: 'if (condition)'
            };
        }
        
        // 기타 (변환 불가능한 것들)
        return {
            type: 'UNKNOWN',
            value: line,
            line: lineNumber,
            pattern: 'unknown'
        };
    }

    // ========================================================================
    // 5. 기계적 매핑: 룰 기반 1:1 변환
    // ========================================================================
    mechanicalMapping(tokens) {
        return tokens.map(token => {
            switch (token.type) {
                case 'FUNCTION_CALL':
                    return this.convertFunctionCall(token);
                    
                case 'ASSIGNMENT':
                    return this.convertAssignment(token);
                    
                case 'CONDITIONAL':
                    return this.convertConditional(token);
                    
                case 'COMMENT':
                    return token; // 주석은 그대로
                    
                default:
                    return {
                        ...token,
                        jsValue: `// TODO: 변환 불가 - ${token.value}`,
                        converted: false
                    };
            }
        });
    }

    // ========================================================================
    // 6. 개별 변환 함수들 (순수 문법 변환만)
    // ========================================================================
    convertFunctionCall(token) {
        const line = token.value;
        
        // npc{name}.{action}({params}) 패턴
        const npcMatch = line.match(/^npc([a-zA-Z가-힣0-9_]+)\.([a-zA-Z가-힣0-9_]+)\((.*)\)$/);
        if (npcMatch) {
            const [, name, action, params] = npcMatch;
            return {
                ...token,
                jsValue: `npc['${name}'].${action}(${params});`,
                converted: true
            };
        }
        
        // environment.{setting}({value}) 패턴
        const envMatch = line.match(/^environment\.([a-zA-Z0-9_]+)\((.*)\)$/);
        if (envMatch) {
            const [, setting, value] = envMatch;
            return {
                ...token,
                jsValue: `environment.${setting}(${value});`,
                converted: true
            };
        }
        
        // sfx.play("{sound}") 패턴
        const sfxMatch = line.match(/^sfx\.play\("(.+)"\)$/);
        if (sfxMatch) {
            const [, sound] = sfxMatch;
            return {
                ...token,
                jsValue: `audioSystem.play('${sound}');`,
                converted: true
            };
        }
        
        // 기본 변환 실패
        return {
            ...token,
            jsValue: `// 변환 실패: ${line}`,
            converted: false
        };
    }

    convertAssignment(token) {
        const line = token.value;
        const match = line.match(/^([a-zA-Z_][a-zA-Z0-9_]*)\s*=\s*(.+)$/);
        
        if (match) {
            const [, variable, value] = match;
            return {
                ...token,
                jsValue: `let ${variable} = ${value};`,
                converted: true
            };
        }
        
        return {
            ...token,
            jsValue: `// 변환 실패: ${line}`,
            converted: false
        };
    }

    convertConditional(token) {
        const line = token.value;
        // 조건문은 거의 그대로 (문법만 확인)
        return {
            ...token,
            jsValue: line.endsWith('{') ? line : line + ' {',
            converted: true
        };
    }

    // ========================================================================
    // 7. JS 코드 조립
    // ========================================================================
    assembleJS(jsTokens) {
        const jsLines = jsTokens.map(token => {
            if (token.type === 'COMMENT') {
                return token.value;
            }
            return token.jsValue || token.value;
        });
        
        return jsLines.join('\n');
    }

    // ========================================================================
    // 8. 문법 검증만 (의미 검증 금지)
    // ========================================================================
    validateSyntax(jsCode) {
        const errors = [];
        const warnings = [];
        
        // 기본 JS 문법 체크
        try {
            // 간단한 파싱 테스트 (실행은 하지 않음)
            new Function(jsCode);
        } catch (syntaxError) {
            errors.push(`JS 문법 오류: ${syntaxError.message}`);
        }
        
        // 괄호 매칭 체크
        const openBraces = (jsCode.match(/\{/g) || []).length;
        const closeBraces = (jsCode.match(/\}/g) || []).length;
        if (openBraces !== closeBraces) {
            errors.push(`괄호 불일치: { ${openBraces}개, } ${closeBraces}개`);
        }
        
        // 기본 경고사항
        if (jsCode.includes('// TODO')) {
            warnings.push('변환되지 않은 PPR 구문이 있습니다');
        }
        
        return {
            valid: errors.length === 0,
            errors: errors,
            warnings: warnings
        };
    }

    // ========================================================================
    // 9. 변환 통계 (디버깅용)
    // ========================================================================
    getConversionStats(tokens) {
        const total = tokens.length;
        const converted = tokens.filter(t => t.converted).length;
        const failed = total - converted;
        
        return {
            total: total,
            converted: converted,
            failed: failed,
            successRate: total > 0 ? (converted / total * 100).toFixed(1) + '%' : '0%'
        };
    }
}

// ============================================================================
// 사용 예시
// ============================================================================

/*
// PPR 입력
const pprCode = `
npc또링.say("안녕하세요!")
environment.setBackground("소울시티")
sfx.play("환영음")
감정상태 = "기쁨"
if (사용자반응 == "좋음") {
    npc또링.smile()
}
`;

// 기계적 변환
const converter = new PPR2JS_MechanicalConverter();
const result = converter.convert(pprCode);

// 결과
console.log("변환 성공:", result.success);
console.log("JS 코드:\n", result.jsCode);
console.log("오류:", result.errors);
console.log("경고:", result.warnings);
*/

// ============================================================================
// 핵심 원칙 재확인
// ============================================================================
/*
1. 감정/AI특성 해석 금지 - 순수 문법 변환만
2. 1:1 매핑 원칙 - 추가적인 "개선"이나 "해석" 금지  
3. 표준 JS 출력 - 모든 AI가 동일한 결과 생성
4. 안전성 우선 - 실행하지 않고 변환만
5. PPR Cross에서 AI 특성 처리 - 역할 분리 명확화

"PPR2JS는 번역기, PPR Cross는 해석기"
*/

module.exports = PPR2JS_MechanicalConverter;