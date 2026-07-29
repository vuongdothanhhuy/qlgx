# Recorded Legacy Rules

## Purpose and decision boundary

This register translates every bullet in
`Source/ChuongTrinh/VersionConfig.xml` into traceable modernization evidence.
It records what a release note implies; it does not decide that the implied
behavior belongs in the new product.

BUG-001 classifies the source evidence. BUG-003 requires a domain expert to
decide whether each rule is still required, changed, or obsolete. No row marked
`Rule — pending BUG-003` may enter implementation as an approved requirement.

## Classification

| Status | Meaning |
|---|---|
| `Rule — pending BUG-003` | The entry states or implies observable behavior, validation, data handling, or output. It remains a hypothesis until domain review. |
| `Cosmetic/unspecified — closed` | The entry is a heading, vague “other fixes” note, or lacks enough behavior to create a rule. Later source/history evidence may add a separate, specific row. |
| `Obsolete implementation — closed` | The entry is tied to the retired Office/Access/desktop implementation rather than a user outcome. Any underlying outcome is captured by another rule row. |

“English / implied rule” is a faithful explanation, not a word-for-word legal
translation. Ellipses and apparent spelling mistakes in the Vietnamese column
are preserved from the source.

## Changelog register

| ID | Version | Source line | Original Vietnamese | English / implied rule | Module | Status |
|---|---:|---:|---|---|---|---|
| CHG-001 | 3.3.7 | 6 | Hiển thị thêm thông tin qua đời trong màn hình danh sách giáo dân. | Show death information in the parishioner list. | People / list | Rule — pending BUG-003 |
| CHG-002 | 3.3.7 | 7 | Hiển thị Tên Cha, Tên Mẹ, Ngày XTRL vào danh sách học sinh giáo lý. | Show father, mother, and First Communion date in the catechism-student list. | Catechism / list | Rule — pending BUG-003 |
| CHG-003 | 3.3.7 | 8 | Sửa một số lỗi khi import dữ liệu. | Unspecified import fixes; no reproducible case is recorded. | Import | Cosmetic/unspecified — closed |
| CHG-004 | 3.3.7 | 9 | Cho phép chọn giáo xứ khi import từ phần mềm MGC nếu trong DataGx.accdb có nhiều hơn một giáo xứ. | When an MGC source contains multiple parishes, require the operator to select the parish to import. | Import / MGC | Rule — pending BUG-003 |
| CHG-005 | 3.3.7 | 10 | Thêm thông tin tên người vợ/chồng vào mẫu in Lý lịch cá nhân. | Include the spouse's name in the personal-profile printout. | Reports / personal profile | Rule — pending BUG-003 |
| CHG-006 | 3.3.7 | 11 | Cho phép nhập ngày bí tích sau ngày hiện tại trong màn hình nhập giáo dân. Chỉ hiện cảnh báo khi lưu dữ liệu giáo dân. | Permit a future sacrament date, but warn when the parishioner record is saved rather than blocking entry. | People / sacraments | Rule — pending BUG-003 |
| CHG-007 | 3.3.7 | 12 | Một số sửa lỗi và cập nhật khác. | Other fixes and updates are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-008 | 3.3.6 | 15 | Thêm thông tin bao đồng, GL Hôn nhân vào trong chi tiết giáo dân. | Show Solemn Communion and marriage-catechism information in parishioner details. | People / catechism | Rule — pending BUG-003 |
| CHG-009 | 3.3.6 | 16 | Sửa một số lỗi khi import dữ liệu từ Excel. | Unspecified Excel-import fixes; no reproducible case is recorded. | Import / Excel | Cosmetic/unspecified — closed |
| CHG-010 | 3.3.6 | 17 | Và một số sửa lỗi cùng cải tiến khác. | Other fixes and improvements are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-011 | 3.3.5 | 20 | Sửa lỗi không hiển thị được tên vợ chồng trong màn hình thống kê hôn phối. | Marriage statistics must display both spouses' names. | Statistics / marriage | Rule — pending BUG-003 |
| CHG-012 | 3.3.4 | 23 | Các cải tiến và sửa lỗi: | Heading for improvements and fixes; no independent behavior. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-013 | 3.3.4 | 24 | Màn hình chọn giáo dân (vào gia đình, sổ bí tích hoặc lớp giáo lý...) mặc định sẽ không hiển thị hết tất cả các giáo dân trong giáo họ được chọn mà cho phép người dùng nhập tên giáo dân cần tìm kiếm, chương trình sẽ tìm kiếm trong cả hồ sơ lưu trữ. | Person pickers should start without loading an entire sub-parish; name search spans active and archived people. | People / search | Rule — pending BUG-003 |
| CHG-014 | 3.3.4 | 25 | Màn hình bí tích chi tiết mặc định được sắp xếp theo số bí tích | Sort sacrament-detail records by sacrament number by default. | Sacraments / list | Rule — pending BUG-003 |
| CHG-015 | 3.3.4 | 26 | Cải tiến chức năng nhập dữ liệu từ chương trình MGC để nhập được dữ liệu cho nhiều trường hợp hơn. | MGC import was broadened, but the supported cases are not identified. | Import / MGC | Cosmetic/unspecified — closed |
| CHG-016 | 3.3.4 | 27 | Màn hình tìm kiếm giáo dân được thêm mục "Ngày sinh" và "Địa chỉ". | Parishioner search must support date of birth and address criteria. | People / search | Rule — pending BUG-003 |
| CHG-017 | 3.3.4 | 28 | Thêm địa chỉ và số điện thoại vào một số mẫu báo cáo. | Reports that identify people or households may require address and phone fields; exact templates need report inventory. | Reports | Rule — pending BUG-003 |
| CHG-018 | 3.3.4 | 29 | Thống kê hôn phối bị báo lỗi trong một số trường hợp. | Marriage statistics must complete for the previously failing cases, but no case is recorded. | Statistics / marriage | Rule — pending BUG-003 |
| CHG-019 | 3.3.4 | 30 | Một số trường hợp thống kê không in được danh sách kết quả sau khi thống kê. | Every successful statistics result must remain printable. | Statistics / export | Rule — pending BUG-003 |
| CHG-020 | 3.3.4 | 31 | Và một số sửa lỗi cùng cải tiến khác | Other fixes and improvements are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-021 | 3.3.3 | 34 | Các cải tiến: | Heading for improvements; no independent behavior. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-022 | 3.3.3 | 35 | Cho phép xem gia đình từ màn hình cá nhân giáo dân. | A parishioner record should navigate to its household record. | People / households | Rule — pending BUG-003 |
| CHG-023 | 3.3.3 | 36 | Đánh dấu những thành viên trong gia đình ở ngoài xứ và không tính trong số lượng thành viên gia đình. | Visually identify household members outside the parish and exclude them from the displayed household count. | Households | Rule — pending BUG-003 |
| CHG-024 | 3.3.3 | 37 | Cho phép xóa hình ảnh gia đình, cá nhân. | Authorized users may remove household and person photos. | People / households / photos | Rule — pending BUG-003 |
| CHG-025 | 3.3.3 | 38 | Khi thêm một hôn phối, nếu cặp hôn phối đó chưa có gia đình, chương trình sẽ hỏi có muốn thêm gia đình mới cho cặp hôn phôi mới hay không. | After recording a marriage for a couple without a household, offer to create their household. | Marriage / households | Rule — pending BUG-003 |
| CHG-026 | 3.3.3 | 39 | Sửa mẫu in Phiếu Gia Đình bằng EXCEL và WORD. | The household-sheet templates were changed, but the intended output difference is not recorded. | Reports / household sheet | Cosmetic/unspecified — closed |
| CHG-027 | 3.3.3 | 40 | Khi đánh dấu một người qua đời, chương trình sẽ tìm người vợ/chồng của người đó để đánh dấu người vợ/chồng đó trở về tình trạng độc thân. | When a married person is marked deceased, locate the spouse and return the spouse to a non-married/single status. | People / marriage | Rule — pending BUG-003 |
| CHG-028 | 3.3.3 | 42 | Sửa các lỗi: | Heading for bug fixes; no independent behavior. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-029 | 3.3.3 | 43 | Khi in phiếu gia đình, nếu chọn mục in người chứng hôn phối sẽ xuất hiện báo lỗi. | A household sheet with the marriage-witness option must generate without error. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-030 | 3.3.3 | 44 | Sửa in phiếu lí lịch cá nhân in nhầm dữ liệu số rửa tội vào ô số rước lễ. | The personal profile's First Communion number must not be populated with the baptism number. | Reports / personal profile | Rule — pending BUG-003 |
| CHG-031 | 3.3.3 | 45 | Thống kê hôn phối không hiện được thông tin giáo họ, tên vợ chồng bị thay thế bằng mã giáo dân. | Marriage statistics must show the sub-parish and spouse names, never substitute person IDs for names. | Statistics / marriage | Rule — pending BUG-003 |
| CHG-032 | 3.3.3 | 46 | Thêm giáo dân bị lỗi khi đang lọc trên lưới giáo dân trong màn hình danh sách giáo dân. | Adding a parishioner while the list is filtered must succeed and preserve consistent list state. | People / list | Rule — pending BUG-003 |
| CHG-033 | 3.3.3 | 47 | Với gia đình có nhiều hơn 8 thành viên, in phiếu gia đình sẽ không in ra đủ 8 thành viên. | A household with more than eight members must print the intended first/full set rather than fewer than eight due to the prior bug; pagination policy needs review. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-034 | 3.3.3 | 48 | Thống kê người đã lập gia đình không chính xác. Có trường hợp cá nhân đã có hôn phối nhưng chương trình vẫn không chọn mục "Có gia đình" | A person with a qualifying marriage must be classified consistently as having a family in statistics. | Statistics / marriage | Rule — pending BUG-003 |
| CHG-035 | 3.3.3 | 49 | Cùng một số sửa lỗi và cải tiến khác. | Other fixes and improvements are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-036 | 3.3.2 (2020-03-30) | 52 | Sửa lỗi khi in phiếu gia đình, nếu chọn mục in người chứng hôn phối sẽ xuất hiện báo lỗi. | Duplicate of CHG-029: household-sheet generation with marriage witnesses must not fail. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-037 | 3.3.2 (2020-03-30) | 53 | Sửa lỗi thống kê hôn phối không hiện được thông tin giáo họ, tên vợ chồng bị thay thế bằng mã giáo dân. | Duplicate of CHG-031: show sub-parish and spouse names in marriage statistics. | Statistics / marriage | Rule — pending BUG-003 |
| CHG-038 | 3.3.2 (2020-03-30) | 54 | Và một số lỗi khác. | Other fixes are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-039 | 3.3.2 (2020-03-15) | 57 | Trong gia đình hoặc hôn phối, khi 1 người mất đi, người còn lại sẽ được tự bỏ chọn mục đã lập gia đình (trở thành độc thân). | Duplicate/expansion of CHG-027: a surviving spouse should no longer be flagged as married after the partner dies. | People / marriage | Rule — pending BUG-003 |
| CHG-040 | 3.3.2 (2020-03-15) | 58 | Thêm tùy chọn vào mục "Tùy chọn" cho việc in phiếu gia đình, cho chọn in cả những người đã đưa vào hồ sơ lưu trữ hay không. | Household-sheet generation must provide an explicit option to include or exclude archived people. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-041 | 3.3.2 (2020-03-15) | 59 | Thêm địa chỉ, số điện thoại vào mẫu in phiếu gia đình và các giáy giới thiệu. | Include address and phone in the household sheet and introduction documents. | Reports | Rule — pending BUG-003 |
| CHG-042 | 3.3.2 (2020-03-15) | 60 | Sửa mẫu in phiếu gia đình để cho phép in nơi nhận bí tích bên dưới ngày nhận bí tích. | Household sheets may print the sacrament place beneath each sacrament date. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-043 | 3.3.2 (2020-03-15) | 61 | Cho phép import dữ liệu MGC phiên bản Access 2007 trở lên. Cần cài thêm "Microsoft Access Database Engine 2010 Redistributable" | The desktop importer used the Access Database Engine to read Access 2007+ MGC files. | Import / MGC | Obsolete implementation — closed |
| CHG-044 | 3.3.2 (2020-03-15) | 62 | Sửa một số lỗi khác. | Other fixes are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-045 | 3.3.0 | 65 | Cập nhật chức năng: | Heading for feature updates; no independent behavior. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-046 | 3.3.0 | 66 | Màn hình danh sách gia đình: | Heading for household-list changes; no independent behavior. | Households / list | Cosmetic/unspecified — closed |
| CHG-047 | 3.3.0 | 67 | Khi chọn nhiều gia đình trên lưới, bấm phím "In phiếu gia đình", chương trình sẽ hỏi in chung tất cả các gia đình được chọn vào 1 file hoặc in thành nhiều file riêng. | For multiple selected households, ask whether to produce one combined file or separate files. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-048 | 3.3.0 | 68 | Nếu người nam hoặc người nữ đã qua đời, chương trình sẽ gạch ngang tên trong danh sách gia đình. | Strike through a deceased husband or wife in the household list. | Households / list | Rule — pending BUG-003 |
| CHG-049 | 3.3.0 | 69 | Màn hình nhập gia đình: | Heading for household-entry changes; no independent behavior. | Households / edit | Cosmetic/unspecified — closed |
| CHG-050 | 3.3.0 | 70 | Khi thêm 1 giáo dân vào làm 1 thành viên của 1 gia đình (không phải vai trò là vợ chồng) nhưng giáo dân đó đã thuộc 1 thành viên của 1 gia đình khác, chương trình sẽ đưa ra thông báo "giáo dân … đã thuộc gia đình… vui lòng xóa giáo dân … ra khỏi gia đình … trước khi thêm". | Reject adding a non-spouse household member who already belongs to another household, and identify the conflicting membership. | Households / validation | Rule — pending BUG-003 |
| CHG-051 | 3.3.0 | 71 | Mỗi giáo dân chỉ có thể là người nam (chồng) hoặc người nữ (vợ) của một gia đình mà thôi. Nếu thêm trùng người nam hoặc người nữ vào nhiều gia đình, chương trình sẽ báo lỗi. Trừ khi gia đình cũ đã được đưa vào hồ sơ lưu trữ. | A person may be husband or wife in only one active household; an archived former household is exempt. | Households / validation | Rule — pending BUG-003 |
| CHG-052 | 3.3.0 | 72 | Khi chọn người nam/ người nữ mà đã có hôn phối, chương trình tự chọn người còn lại vào màn hình nhập gia đình kèm theo thông tin hôn phối. | Selecting a married husband/wife for a household should select the spouse and carry the marriage details. | Households / marriage | Rule — pending BUG-003 |
| CHG-053 | 3.3.0 | 73 | Màn hình hội đoàn: ngày bổn mạng chỉ cần nhập ngày tháng, không nhập năm | An association patronal feast date stores day and month without requiring a year. | Associations / partial dates | Rule — pending BUG-003 |
| CHG-054 | 3.3.0 | 74 | Chứng nhận hôn phối thêm tùy chọn in ngày rao hôn phối, thêm thông tin bí tích thêm sức, tên giáo họ và địa chỉ của từng người trong đôi hôn phối. | Marriage certificates may include bann dates and must support confirmation, sub-parish, and address details for each spouse. | Reports / marriage certificate | Rule — pending BUG-003 |
| CHG-055 | 3.3.0 | 75 | Màn hình lớp giáo lý: cho sửa số thứ tự học viên trong lớp. | Allow editing a student's ordering number within a catechism class. | Catechism / class | Rule — pending BUG-003 |
| CHG-056 | 3.3.0 | 76 | Các mẫu in bằng file WORD, bỏ các dấu ...... (trong tương lai, sẽ cố gắng để dấu ...... trong trường hợp không có dữ liệu tại mục cần in). | The Word templates removed dotted placeholders; this formatting technique is not a modern requirement. | Reports / Word templates | Obsolete implementation — closed |
| CHG-057 | 3.3.0 | 77 | Thêm địa chỉ vào màn hình Tìm giáo dân | Show address in parishioner search. | People / search | Rule — pending BUG-003 |
| CHG-058 | 3.3.0 | 79 | Sửa lỗi: | Heading for bug fixes; no independent behavior. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-059 | 3.3.0 | 80 | Màn hình thống kê: Khi có kết quả thống kê, nút "In danh sách" bị vô hiệu hóa, không bấm vào được. | Enable printing whenever statistics returned a printable result. | Statistics / export | Rule — pending BUG-003 |
| CHG-060 | 3.3.0 | 81 | Màn hình nhập giáo dân: Không lưu được số CMND | Persist the person's identity-card number when the record is saved. | People / identity | Rule — pending BUG-003 |
| CHG-061 | 3.3.0 | 82 | Màn hình lớp giáo lý: Không xóa được học viên ra khỏi lớp giáo lý. | Authorized users must be able to remove a student from a catechism class. | Catechism / class | Rule — pending BUG-003 |
| CHG-062 | 3.3.0 | 83 | Sửa một số lỗi gây ra việc nhập dữ liệu từ Excel không thành công. | A valid Excel import should complete, but the previously failing cases are not identified. | Import / Excel | Rule — pending BUG-003 |
| CHG-063 | 3.3.0 | 85 | Cùng một số chỉnh sửa và cải tiến khác. | Other adjustments and improvements are not specified. | Cross-cutting | Cosmetic/unspecified — closed |
| CHG-064 | 3.2.2 | 89 | Thêm chức năng quản lý Hội đoàn. | Provide association management. Detailed behavior must come from source/workflow inventory. | Associations | Rule — pending BUG-003 |
| CHG-065 | 3.2.2 | 91 | Cập nhật chức năng quản lý thông tin Giáo Dân. | Heading for parishioner-management changes; no independent behavior. | People | Cosmetic/unspecified — closed |
| CHG-066 | 3.2.2 | 92 | Thêm ràng buộc nếu đã có nhập ngày rửa tội thì bắt buộc phải có tên thánh. | If a baptism date is entered, baptismal/saint name is required. | People / sacraments / validation | Rule — pending BUG-003 |
| CHG-067 | 3.2.2 | 93 | Thêm 1 tab Hội đoàn có chức năng thêm giáo dân vào hội đoàn hoặc xem lại lịch sử tham gia hội đoàn của giáo dân. | From a parishioner record, add association membership and view membership history. | People / associations | Rule — pending BUG-003 |
| CHG-068 | 3.2.2 | 94 | Sửa lỗi bấm phím tab ở các ô nhập ngày tháng, con trỏ không nhảy đến ô nhập tiếp theo. | Date inputs must participate correctly in keyboard tab order. | UI / date input | Rule — pending BUG-003 |
| CHG-069 | 3.2.2 | 95 | Sửa lỗi một số trường hợp một giáo dân đã được chọn lập gia đình nhưng mục "Đã có gia đình" không được chọn trong màn hình thông tin giáo dân. | A qualifying household/marriage selection must keep the person's “has family” status consistent. | People / households | Rule — pending BUG-003 |
| CHG-070 | 3.2.2 | 97 | Cập nhật chức năng quản lý thông tin Gia Đình. | Heading for household-management changes; no independent behavior. | Households | Cosmetic/unspecified — closed |
| CHG-071 | 3.2.2 | 98 | Sửa lỗi khi người nam trong gia đình đã mất, chọn người nữ làm chủ hộ, chương trình luôn thông báo chọn chủ hộ khi cập nhật gia đình. | A surviving wife selected as head must satisfy household-head validation. | Households / validation | Rule — pending BUG-003 |
| CHG-072 | 3.2.2 | 99 | Sửa lỗi khi chọn một thành viên trong gia đình lên làm chủ hộ, chương trình hiển thị lỗi khi lưu. | Promoting an eligible household member to head must save successfully. | Households / validation | Rule — pending BUG-003 |
| CHG-073 | 3.2.2 | 100 | Ràng buộc số tuổi phải lớn hơn 14 tuổi mới cho phép hôn phối hoặc thêm vào làm người nam, người nữ trong gia đình. Nếu người nam hoặc người nữ dưới 18 tuổi sẽ hiển thị cảnh báo. | Block marriage/husband/wife assignment at age 14 or younger; warn when either spouse is under 18. Exact age calculation and policy require review. | Marriage / households / validation | Rule — pending BUG-003 |
| CHG-074 | 3.2.2 | 101 | Thêm một số ràng buộc khác về việc một thành viên thuộc nhiều gia đình. | Other multi-household membership constraints are not specified. | Households / validation | Cosmetic/unspecified — closed |
| CHG-075 | 3.2.2 | 102 | Chỉ gạch bỏ những thành viên trong gia đình nếu thành viên đó đã được lập một gia đình mới với vai trò là người nam hoặc người nữ trong màn hình nhập gia đình. | Strike through a household member only when the person is husband or wife in a newly created household. | Households / list | Rule — pending BUG-003 |
| CHG-076 | 3.2.2 | 103 | Khi thêm người nam hoặc người nữ vào màn hình nhập gia đình, nếu không có thông tin hôn phối thì không tự động cập nhật trạng thái đã lập gia đình cho người nam hoặc người nữ. | Assigning someone as husband/wife without marriage information must not automatically mark that person as married. | Households / marriage | Rule — pending BUG-003 |
| CHG-077 | 3.2.2 | 104 | Cập nhật lại chức năng chuyển xứ cho gia đình: Nếu thành viên gia đình đã có gia đình riêng hoặc đã vào danh sách lưu trữ thì sẽ không chuyển xứ cùng gia đình. | During household transfer, do not transfer members who have their own household or are archived. | Transfers / households | Rule — pending BUG-003 |
| CHG-078 | 3.2.2 | 105 | Cập nhật lại cách tính số lượng thành viên trong gia đình ở màn hình danh sách gia đình. | Heading for the household-count rules in CHG-079 and CHG-080. | Households / count | Cosmetic/unspecified — closed |
| CHG-079 | 3.2.2 | 106 | Đếm tất cả các thành viên với mọi vai trò. Trước đây chương trình chỉ tính các thành viên có vài trò là con. | Count eligible household members in every role, not only children. | Households / count | Rule — pending BUG-003 |
| CHG-080 | 3.2.2 | 107 | Không đếm các thành viên đã chuyển xứ, qua đời, có gia đình và ở trong gia đình khác (bị gạch đỏ trong danh sách thành viên gia đình). | Exclude transferred, deceased, or separately housed/married members from the household count. | Households / count | Rule — pending BUG-003 |
| CHG-081 | 3.2.2 | 109 | Quản lý giáo lý: | Heading for catechism changes; no independent behavior. | Catechism | Cosmetic/unspecified — closed |
| CHG-082 | 3.2.2 | 110 | Cho phép cập nhật thông tin giáo dân cho danh sách các học viên trong một lớp giáo lý. | Allow updating parishioner information for students listed in a catechism class. | Catechism / people | Rule — pending BUG-003 |
| CHG-083 | 3.2.2 | 112 | Màn hình thống kê: | Heading for statistics changes; no independent behavior. | Statistics | Cosmetic/unspecified — closed |
| CHG-084 | 3.2.2 | 113 | Sửa lỗi sau khi thống kê xong thì không xuất được danh sách kết qua ra Excel. | A completed statistics result must be exportable. The modern format is decided separately. | Statistics / export | Rule — pending BUG-003 |
| CHG-085 | 3.2.1 | 116 | Sửa lỗi khi cập nhật gia đình/giáo dân thì bị mất hình ảnh đã lưu trước đó. | Updating a person or household must preserve its existing photo unless removal/replacement is explicit. | People / households / photos | Rule — pending BUG-003 |
| CHG-086 | 3.2.1 | 117 | Chỉnh sửa mẫu in "Phiếu gia đình" trên MS Word cho giống với các mẫu trên MS Excel. | Household-sheet outputs should have equivalent required content/layout across supported output paths. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-087 | 3.2.1 | 118 | Trước đây, các tùy chọn cho phiếu gia đình chỉ có tác dụng với mẫu trên MS Excel. | Household-sheet options should affect every supported output path consistently. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-088 | 3.2.1 | 119 | Sửa lỗi mẫu in "Phiếu gia đình" trên MS Word không in ra được Mã giáo dân khi chọn tùy chọn "Thay số thứ tự bằng mã giáo dân". | When configured to replace sequence number with person ID, the household sheet must print the person ID. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-089 | 3.2.1 | 120 | Thêm các tùy chọn cho "Phiếu gia đình" như: | Heading for household-sheet options in CHG-090 through CHG-093. | Reports / household sheet | Cosmetic/unspecified — closed |
| CHG-090 | 3.2.1 | 121 | In số bí tích bên dưới ngày nhận bí tích. | Household sheets may print each sacrament number beneath its date. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-091 | 3.2.1 | 122 | In người đã đầu bên dưới ngày nhận bí tích. | Household sheets may print the sponsor beneath the sacrament date. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-092 | 3.2.1 | 123 | In người chứng hôn phối bên dưới ngày hôn phối. | Household sheets may print marriage witnesses beneath the marriage date. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-093 | 3.2.1 | 124 | In tên cha mẹ (chỉ có với mẫu MS Excel). | Household sheets may print parents' names; the legacy limitation to Excel is not preserved. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-094 | 3.2.1 | 125 | Phiếu gia đình luôn in ra các dòng trống cho đủ 8 dòng nếu số lượng thành viên trong gia đình không đủ 8 người. | A household sheet pads the member area to eight rows when fewer than eight members are printed. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-095 | 3.2.1 | 126 | Hiện tại mẫu in trên MS Word chưa được hoàn hảo như mẫu in trên Excel, tốc độ xuất phiếu gia đình trên MS Word cũng chậm hơn, khuyến khích sử dụng mẫu Excel cho phiếu gia đình. | Word was slower and less complete than Excel, so Excel was recommended; this implementation comparison is retired. | Reports / Office implementation | Obsolete implementation — closed |
| CHG-096 | 3.2 | 129 | Sửa lỗi in Phiếu gia đình in cả những người đã đưa vào hồ sơ lưu trữ hoặc đã lập gia đình. | By default, exclude archived members and members who formed another family from the household sheet. | Reports / household sheet | Rule — pending BUG-003 |
| CHG-097 | 3.2 | 130 | Sửa mẫu in Phiếu gia đình trên MS Word. | The Word template was changed, but the intended output difference is not recorded. | Reports / Word templates | Cosmetic/unspecified — closed |
| CHG-098 | 3.2 | 131 | Cho phép chọn mẫu in Phiếu gia đình bằng MS Word hoặc MS Excel. | Choosing between Word and Excel templates is a retired desktop implementation choice. | Reports / Office implementation | Obsolete implementation — closed |
| CHG-099 | 3.1 | 134 | Cho phép quản trị người dùng với nhiều vai trò khác nhau. | Support user administration with multiple roles. Role definitions and permissions require separate review. | Accounts / authorization | Rule — pending BUG-003 |
| CHG-100 | 3.1 | 135 | Cập nhật tính năng hiển thị ảnh cho cá nhân và gia đình. | Display photos for people and households. | People / households / photos | Rule — pending BUG-003 |
| CHG-101 | 3.1 | 136 | Sửa lỗi đếm số thành viên trong gia đình (loại bỏ những thành viên đã có gia đình hoặc đã mất) | Exclude members who formed another family or died from the household count. | Households / count | Rule — pending BUG-003 |
| CHG-102 | 3.1 | 137 | Cập nhật thêm một số loại thống kê : | Heading for statistics types in CHG-103 through CHG-109. | Statistics | Cosmetic/unspecified — closed |
| CHG-103 | 3.1 | 138 | Danh sách chủ hộ | Provide a list/statistic of household heads. | Statistics / households | Rule — pending BUG-003 |
| CHG-104 | 3.1 | 139 | Danh sách gia trưởng | Provide the legacy “gia trưởng” demographic list; exact definition requires domain review. | Statistics / demographics | Rule — pending BUG-003 |
| CHG-105 | 3.1 | 140 | Danh sách hiền mẫu | Provide the legacy “hiền mẫu” demographic list; exact definition requires domain review. | Statistics / demographics | Rule — pending BUG-003 |
| CHG-106 | 3.1 | 141 | Danh sách cao niên | Provide the senior/elderly people list; age threshold and date precision require review. | Statistics / demographics | Rule — pending BUG-003 |
| CHG-107 | 3.1 | 142 | Danh sách giới trẻ | Provide the youth list; age threshold and date precision require review. | Statistics / demographics | Rule — pending BUG-003 |
| CHG-108 | 3.1 | 143 | Danh sách thiếu nhi | Provide the children list; age threshold and date precision require review. | Statistics / demographics | Rule — pending BUG-003 |
| CHG-109 | 3.1 | 144 | Danh sách gia đình theo từng tình trạng hôn phối | Provide household lists grouped/filtered by marriage status. | Statistics / households | Rule — pending BUG-003 |
| CHG-110 | 3.1 | 145 | Cập nhật tính năng : quản lý tài khoản người dùng, bảo mật | Provide account administration and security, but the entry does not define controls or permissions. | Accounts / security | Rule — pending BUG-003 |
| CHG-111 | 3.1 | 146 | Bổ sung cho chức năng "Giới thiệu hôn phối": khi làm giấy giới thiệu hôn phối, đối tượng được giới thiệu sẽ được thêm vào danh sách giáo dân nếu người đó chưa có thông tin trong giáo xứ | While creating a marriage-introduction document, create the introduced person in the parishioner register if no record exists. | Marriage introduction / people | Rule — pending BUG-003 |
| CHG-112 | 3.1 | 147 | Chỉnh sửa "Trình độ văn hóa". | Education-level handling was changed, but no behavior or values are recorded. | People / reference data | Cosmetic/unspecified — closed |
| CHG-113 | 3.1 | 148 | Bổ sung "Trình độ chuyên môn", "Ngoại ngữ". | Store professional qualification and foreign-language information for a person. | People / profile | Rule — pending BUG-003 |
| CHG-114 | 3.1 | 149 | Chỉnh sửa hôn phối giáo dân. | Parishioner marriage handling was changed, but no behavior is recorded. | Marriage | Cosmetic/unspecified — closed |
| CHG-115 | 3.1 | 150 | Cho phép in phiếu lý lịch cá nhân hàng loạt | Allow batch generation of personal-profile sheets. | Reports / personal profile | Rule — pending BUG-003 |
| CHG-116 | 3.1 | 151 | Cho phép in phiếu lý gia đình hàng loạt | Allow batch generation of household sheets. | Reports / household sheet | Rule — pending BUG-003 |

## Duplicate and relationship index

Duplicates are retained because BUG-001 requires one row per recorded entry:

| Relationship | Rows | Treatment |
|---|---|---|
| Surviving-spouse status | CHG-027, CHG-039 | Same core rule stated in two releases; review together |
| Household-sheet marriage witnesses error | CHG-029, CHG-036 | Exact repeated fix; one future characterization behavior may cite both |
| Marriage-statistics names/sub-parish | CHG-031, CHG-037 | Exact repeated fix; one future characterization behavior may cite both |
| Household “has family” consistency | CHG-034, CHG-069 | Related symptoms in statistics and person detail; validate one domain invariant with separate UI outcomes |
| Household count exclusions | CHG-023, CHG-079, CHG-080, CHG-101 | Later entries refine role inclusion and exclusion conditions; do not collapse before domain review |
| Statistics printing/export | CHG-019, CHG-059, CHG-084 | Same result-availability concern across print and Excel export paths |
| Household-sheet output parity | CHG-086, CHG-087 | Template parity and option parity are distinct but should share one report contract |

## BUG-003 review contract

For every `Rule — pending BUG-003` row, the domain reviewer must record:

1. `Still required`, `Changed`, or `Obsolete`;
2. the approved Vietnamese business statement and English equivalent;
3. a concrete valid example and error/edge example;
4. which roles may view or perform the behavior;
5. the source fields and partial/unknown states involved;
6. the future characterization-test ID, or a reason no automated rule applies.

Review related rows together but never delete their source traceability. A
changed rule keeps the original wording and adds the approved replacement.

## Completion checks

| Check | Result |
|---|---|
| XML bullet lines | 116 |
| Register rows | 116 (`CHG-001` through `CHG-116`) |
| Source versions/releases | 12 dated or named release sections represented |
| Unclassified rows | 0 |
| Current-policy decisions made by BUG-001 | 0; all behavioral rows remain pending BUG-003 |
