<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>생기부 나이스 - 통합 관리 시스템</title>
    <!-- 외부 라이브러리 로드 -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://unpkg.com/react@18/umd/react.production.min.js"></script>
    <script src="https://unpkg.com/react-dom@18/umd/react-dom.production.min.js"></script>
    <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
    <script src="https://unpkg.com/lucide@latest"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Noto+Sans+KR:wght@300;400;700;900&display=swap');
        body { font-family: 'Noto Sans KR', sans-serif; margin: 0; background-color: #f1f5f9; }
        .document-shadow { box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.1), 0 8px 10px -6px rgba(0, 0, 0, 0.1); }
        .custom-scrollbar::-webkit-scrollbar { width: 4px; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
    </style>
</head>
<body>
    <div id="root"></div>

    <script type="text/babel">
        const { useState, useEffect } = React;

        // 아이콘 컴포넌트
        const LucideIcon = ({ name, size = 20, className = "" }) => {
            useEffect(() => {
                if (window.lucide) window.lucide.createIcons();
            }, [name]);
            return <i data-lucide={name} className={className} style={{ width: size, height: size, display: 'inline-block' }}></i>;
        };

        const App = () => {
            const [step, setStep] = useState('auth');
            const [activeTab, setActiveTab] = useState('all');
            const [student, setStudent] = useState({ name: '', school: '' });
            const [data, setData] = useState({
                subjects: [
                    { id: 1, title: '수학 I', text: '수학적 추론 능력이 뛰어나며 복잡한 함수 문제를 논리적으로 해결함.' },
                    { id: 2, title: '국어', text: '비판적 읽기 능력이 우수하며 작가의 의도를 다각도에서 분석함.' }
                ],
                behavior: '학급의 리더로서 책임감이 강하고 타인을 배려하는 태도가 돋보임.'
            });

            // 맞춤법 검사 로직
            const checkText = (text) => {
                const results = [];
                if (text && !text.endsWith('.')) results.push({ type: 'err', msg: '마침표(.) 누락' });
                if (text.includes('매우') || text.includes('너무')) results.push({ type: 'warn', msg: '주관적 표현 지양' });
                if (text.includes('탁월함')) results.push({ type: 'tip', msg: '구체적 사례 추가 권장' });
                return results;
            };

            if (step === 'auth') {
                return (
                    <div className="min-h-screen flex items-center justify-center bg-slate-900 p-6">
                        <div className="bg-white p-10 rounded-[2rem] w-full max-w-md shadow-2xl">
                            <div className="text-center mb-10">
                                <div className="w-16 h-16 bg-blue-600 rounded-2xl flex items-center justify-center text-white mx-auto mb-4">
                                    <LucideIcon name="shield-check" size={32} />
                                </div>
                                <h1 className="text-2xl font-black text-slate-800">생기부 나이스</h1>
                                <p className="text-slate-400 text-sm mt-1">통합 관리 시스템 접속</p>
                            </div>
                            <div className="space-y-4">
                                <input className="w-full p-4 bg-slate-50 border border-slate-100 rounded-xl outline-none focus:ring-2 ring-blue-500" placeholder="이름" value={student.name} onChange={e => setStudent({...student, name: e.target.value})} />
                                <input className="w-full p-4 bg-slate-50 border border-slate-100 rounded-xl outline-none focus:ring-2 ring-blue-500" placeholder="학교명" value={student.school} onChange={e => setStudent({...student, school: e.target.value})} />
                                <button onClick={() => setStep('main')} className="w-full bg-blue-600 text-white font-bold py-4 rounded-xl hover:bg-blue-700 transition-all">접속하기</button>
                            </div>
                        </div>
                    </div>
                );
            }

            return (
                <div className="flex h-screen overflow-hidden">
                    {/* 사이드바 */}
                    <div className="w-64 bg-slate-900 text-white p-6 flex flex-col shrink-0">
                        <div className="mb-10 bg-white/5 p-4 rounded-xl border border-white/10">
                            <p className="text-blue-400 font-bold text-sm">{student.name || '미입력'}</p>
                            <p className="text-slate-500 text-[10px]">{student.school || '학교 미지정'}</p>
                        </div>
                        <nav className="space-y-2">
                            <button onClick={() => setActiveTab('all')} className={`w-full text-left px-4 py-3 rounded-lg flex items-center gap-3 text-sm font-medium ${activeTab === 'all' ? 'bg-blue-600' : 'hover:bg-white/5'}`}><LucideIcon name="eye" size={16}/>전체 열람</button>
                            <button onClick={() => setActiveTab('sub')} className={`w-full text-left px-4 py-3 rounded-lg flex items-center gap-3 text-sm font-medium ${activeTab === 'sub' ? 'bg-blue-600' : 'hover:bg-white/5'}`}><LucideIcon name="book" size={16}/>교과 세특</button>
                            <button onClick={() => setActiveTab('beh')} className={`w-full text-left px-4 py-3 rounded-lg flex items-center gap-3 text-sm font-medium ${activeTab === 'beh' ? 'bg-blue-600' : 'hover:bg-white/5'}`}><LucideIcon name="user" size={16}/>행동 특성</button>
                        </nav>
                    </div>

                    {/* 메인 뷰 */}
                    <div className="flex-1 bg-slate-200 overflow-y-auto p-12 flex justify-center gap-8">
                        {/* 문서 영역 */}
                        <div className="w-[800px] min-h-[1100px] bg-white document-shadow p-[80px] shrink-0">
                            <h2 className="text-center text-3xl font-black mb-20 underline underline-offset-8 decoration-1 tracking-widest">학 교 생 활 기 록 부</h2>
                            
                            <div className="space-y-12 text-xs">
                                <section>
                                    <h3 className="font-bold mb-3 flex items-center gap-2"><span className="w-1 h-3 bg-blue-600"></span>1. 인적사항</h3>
                                    <table className="w-full border border-slate-300">
                                        <tbody>
                                            <tr>
                                                <td className="border border-slate-300 bg-slate-50 p-2 w-20 font-bold text-center">성명</td>
                                                <td className="border border-slate-300 p-2">{student.name}</td>
                                                <td className="border border-slate-300 bg-slate-50 p-2 w-20 font-bold text-center">학교명</td>
                                                <td className="border border-slate-300 p-2">{student.school}</td>
                                            </tr>
                                        </tbody>
                                    </table>
                                </section>

                                {(activeTab === 'all' || activeTab === 'sub') && (
                                    <section>
                                        <h3 className="font-bold mb-3 flex items-center gap-2"><span className="w-1 h-3 bg-blue-600"></span>2. 교과 세부능력 및 특기사항</h3>
                                        <div className="border border-slate-300">
                                            {data.subjects.map(s => (
                                                <div key={s.id} className="flex border-b border-slate-300 last:border-0">
                                                    <div className="w-24 bg-slate-50 p-4 font-bold text-center flex items-center justify-center border-r border-slate-300">{s.title}</div>
                                                    <textarea 
                                                        className="flex-1 p-4 outline-none resize-none min-h-[100px] focus:bg-blue-50/30"
                                                        value={s.text}
                                                        onChange={e => {
                                                            const next = data.subjects.map(item => item.id === s.id ? {...item, text: e.target.value} : item);
                                                            setData({...data, subjects: next});
                                                        }}
                                                    />
                                                </div>
                                            ))}
                                        </div>
                                    </section>
                                )}

                                {(activeTab === 'all' || activeTab === 'beh') && (
                                    <section>
                                        <h3 className="font-bold mb-3 flex items-center gap-2"><span className="w-1 h-3 bg-blue-600"></span>3. 행동특성 및 종합의견</h3>
                                        <div className="border border-slate-300 p-4">
                                            <textarea 
                                                className="w-full min-h-[200px] outline-none resize-none focus:bg-blue-50/30"
                                                value={data.behavior}
                                                onChange={e => setData({...data, behavior: e.target.value})}
                                            />
                                        </div>
                                    </section>
                                )}
                            </div>
                        </div>

                        {/* 피드백 패널 */}
                        <div className="w-80 shrink-0 sticky top-0 h-fit">
                            <div className="bg-white rounded-2xl shadow-lg border border-slate-100 p-6">
                                <h4 className="font-bold text-slate-800 mb-4 flex items-center gap-2 text-sm">
                                    <LucideIcon name="sparkles" className="text-blue-600" size={18} />실시간 진단
                                </h4>
                                <div className="space-y-4 max-h-[700px] overflow-y-auto custom-scrollbar pr-2">
                                    {[...data.subjects, {id: 'beh', title: '행동특성', text: data.behavior}].map(item => {
                                        const errors = checkText(item.text);
                                        if (errors.length === 0) return null;
                                        return (
                                            <div key={item.id} className="p-4 bg-slate-50 rounded-xl border border-slate-100">
                                                <p className="text-[10px] font-bold text-blue-500 mb-2">{item.title}</p>
                                                {errors.map((e, idx) => (
                                                    <div key={idx} className="flex items-start gap-2 text-[11px] mb-1 text-slate-600">
                                                        <span className={`w-1 h-1 rounded-full mt-1.5 shrink-0 ${e.type === 'err' ? 'bg-red-500' : 'bg-amber-500'}`}></span>
                                                        {e.msg}
                                                    </div>
                                                ))}
                                            </div>
                                        );
                                    })}
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            );
        };

        const root = ReactDOM.createRoot(document.getElementById('root'));
        root.render(<App />);
    </script>
</body>
</html>
